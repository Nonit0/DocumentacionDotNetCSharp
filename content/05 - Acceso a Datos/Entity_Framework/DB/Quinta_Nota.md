---
title: "📝 Nota 05: DBContext Base - Parte 1"
tags:
  - csharp9
  - dotnet5/seguridad
  - performance
  - basico
  - arquitectura/patrones
  - efcore5
  - infraestructura
draft: false
---
# Patrón de Múltiples DbContexts (Core DB vs Custom DB)

### Qué es y para qué sirve

En arquitecturas escalables, concentrar todas las tablas del sistema en un único ApplicationDbContext monolítico viola el principio de Responsabilidad Única. La solución profesional es usar múltiples DbContexts dentro de la misma base de datos MariaDB:

1. IdentityContext (Custom DB para Seguridad): Gestiona exclusivamente la seguridad, usuarios y roles (esquema AspNet*). -> Ver [[DbContext_Identity_Seguridad]]
    
2. BazarDbContext (DB del Core): Gestiona el núcleo del negocio puro: Productos, Órdenes, etc.
    

### Cuándo usarlo en proyectos reales

Este patrón, alineado con los Bounded Contexts de [[Domain-Driven Design]], permite realizar migraciones de seguridad sin afectar el catálogo comercial. Es el paso previo natural si la plataforma necesita evolucionar hacia microservicios en el futuro.

### Buenas prácticas

Dado que ambos contextos atacan al mismo servidor MariaDB, es obligatorio separar las tablas del historial de migraciones de EF Core. De lo contrario, IdentityContext y BazarDbContext colisionarán al intentar leer y escribir en la tabla por defecto __EFMigrationsHistory. Esto se configura en Startup.cs.

### Errores comunes de juniors y cómo evitarlos

Error: Intentar hacer un .Include() o un JOIN con LINQ cruzando un ApplicationUser del IdentityContext y una Order del BazarDbContext.

Consecuencia: EF Core lanzará una excepción indicando que no puede traducir la consulta, ya que los DbContexts son entidades lógicas aisladas.

Solución: Guardar el CustomerId como un simple string de 36 caracteres en el modelo de Órdenes y cruzar los datos en memoria en la capa de Casos de Uso (ver [[Patrón CQRS]]).

### Ejemplo de código: Aislamiento en Inyección de Dependencias (.NET 5)
```csharp
using Microsoft.EntityFrameworkCore;  
using Microsoft.Extensions.Configuration;  
using Microsoft.Extensions.DependencyInjection;  
using Pomelo.EntityFrameworkCore.MySql.Infrastructure;  
  
namespace Bazar.Infrastructure.DependencyInjection  
{  
    public static class PersistenceServiceCollectionExtensions  
    {  
        // Método de extensión llamado desde Startup.cs  
        public static IServiceCollection AddBazarPersistence(this IServiceCollection services, IConfiguration config)  
        {  
            var connectionString = config.GetConnectionString("MariaDb");  
            var serverVersion = ServerVersion.AutoDetect(connectionString);  
  
            // 1. Contexto de Negocio (Core)  
            services.AddDbContext<BazarDbContext>(options =>  
                options.UseMySql(connectionString, serverVersion, mySqlOptions =>  
                {  
                    // Aislamos la tabla de migraciones para evitar colisiones  
                    mySqlOptions.MigrationsHistoryTable("__EFMigrationsHistory_Domain");  
                }));  
  
            // 2. Contexto de Seguridad (Identity)  
            services.AddDbContext<IdentityContext>(options =>  
                options.UseMySql(connectionString, serverVersion, mySqlOptions =>  
                {  
                    // Tabla de migraciones exclusiva para Seguridad  
                    mySqlOptions.MigrationsHistoryTable("__EFMigrationsHistory_Identity");  
                }));  
  
            return services;  
        }  
    }  
}
```
