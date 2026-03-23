---
title: "📝 Nota 12: Entidad Base Auditable"
tags:
  - csharp9
  - dotnet5/memoria
  - performance
  - arquitectura/ddd
  - efcore5
  - dotnet5/patrones
draft: false
---
# Entidad Base Auditable (DRY en el Dominio)

### Qué es y para qué sirve

En sistemas empresariales, casi todas las tablas de la base de datos necesitan un rastro de auditoría: saber _cuándo_ se creó un registro, _quién_ lo creó, _cuándo_ se modificó por última vez y _quién_ lo modificó. En lugar de repetir estas cuatro propiedades (`CreatedAt`, `CreatedBy`, `ModifiedAt`, `ModifiedBy`) en cada una de las clases de tu dominio (violando el principio DRY - _Don't Repeat Yourself_), creamos una **Clase Base Abstracta**. Todas las entidades de nuestro dominio heredarán de ella. En el ecosistema Java (Hibernate) esto es similar a usar `@MappedSuperclass` con `@EntityListeners`. En C# y.NET 5, lo resolvemos interceptando el momento de guardado en el ORM ([[Entity Framework]]).

### Cuándo usarlo en proyectos reales

Se utiliza por defecto en cualquier aplicación empresarial (como tu _E-Commerce Bazar_). Es un requisito indispensable para cumplir con normativas de trazabilidad de datos (como la GDPR o normativas bancarias), asegurando que ninguna inserción o actualización pase desapercibida.

### Buenas prácticas

- **Aislamiento de la Identidad:** La entidad base no debe saber de dónde viene el usuario. En lugar de acoplar la clase directamente a la sesión web HTTP, inyectamos una interfaz como `ICurrentUserService` en el `DbContext` para que nos devuelva el ID del usuario extraído del [[JWT_Fundamentos|JWT]].
    
- **Automatización en el DbContext:** La lógica para actualizar las fechas de modificación no se debe hacer a mano. Se debe sobrescribir el método `SaveChangesAsync()` del `BazarDbContext` para que.NET 5 inspeccione qué entidades han cambiado y actualice sus fechas automáticamente justo antes de generar el SQL.
    

### Errores comunes de juniors y cómo evitarlos

**Error:** El desarrollador Junior asigna manualmente `entity.CreatedAt = DateTime.UtcNow;` y `entity.CreatedBy = userId;` dentro de cada uno de los métodos de sus [[Controladores y Servicios]]. **Consecuencia:** Código altamente repetitivo. Tarde o temprano, a un desarrollador se le olvidará asignar la fecha de modificación en un endpoint específico, corrompiendo la auditoría de la base de datos de forma silenciosa. **Solución:** Automatizarlo en la capa de Infraestructura, centralizando la responsabilidad en el `DbContext`.

### Ejemplo de código: Implementación Limpia
```csharp
// 1. LA CAPA DE DOMINIO (04 - Dominio y Entidades)
using System;

namespace Bazar.Domain.Common
{
    // Clase abstracta: No se puede instanciar por sí sola, solo se hereda.
    public abstract class AuditableEntity
    {
        // Usamos string para mantener compatibilidad con nuestros UUIDs de 36 caracteres
        public string CreatedBy { get; set; }
        public DateTime CreatedAt { get; set; }
        public string LastModifiedBy { get; set; }
        public DateTime? LastModifiedAt { get; set; }
    }
}

// Ejemplo de uso en una entidad (Hereda la auditoría)
namespace Bazar.Domain.Entities
{
    public class Product : AuditableEntity
    {
        public string Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}

// 2. LA CAPA DE INFRAESTRUCTURA (Sobrescribiendo el guardado en EF Core 5)
using Microsoft.EntityFrameworkCore;
using System;
using System.Threading;
using System.Threading.Tasks;
using Bazar.Domain.Common;

namespace Bazar.Infrastructure.Persistence
{
    public class BazarDbContext : DbContext
    {
        // Simulamos un servicio inyectado que lee el JWT del usuario actual
        private readonly ICurrentUserService _currentUserService;

        public BazarDbContext(
            DbContextOptions<BazarDbContext> options, 
            ICurrentUserService currentUserService) : base(options)
        {
            _currentUserService = currentUserService;
        }

        public DbSet<Product> Products { get; set; }

        // Interceptamos el guardado en la base de datos
        public override Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
        {
            // ChangeTracker vigila todas las entidades en memoria
            foreach (var entry in ChangeTracker.Entries<AuditableEntity>())
            {
                switch (entry.State)
                {
                    case EntityState.Added:
                        entry.Entity.CreatedBy = _currentUserService.UserId;
                        entry.Entity.CreatedAt = DateTime.UtcNow;
                        break;

                    case EntityState.Modified:
                        entry.Entity.LastModifiedBy = _currentUserService.UserId;
                        entry.Entity.LastModifiedAt = DateTime.UtcNow;
                        break;
                }
            }

            return base.SaveChangesAsync(cancellationToken);
        }
    }
}
```