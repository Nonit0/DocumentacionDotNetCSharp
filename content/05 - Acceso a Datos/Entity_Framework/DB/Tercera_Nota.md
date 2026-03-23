---
title: "📝 Nota 03: DbContext Configuration"
tags:
  - csharp9
  - dotnet5/memoria
  - performance
  - basico
  - mariadb
  - infraestructura
  - optimización
  - efcore5
draft: false
aliases:
  - Configuración EF Core
  - Fluent API
---
# Optimización de MariaDB y UUIDs con EF Core 5

### Qué es y para qué sirve

Entity Framework Core 5 es el ORM predilecto de.NET 5. Para interactuar con motores MySQL/MariaDB, se emplea el adaptador Pomelo.EntityFrameworkCore.MySql.

Nuestra arquitectura exige el uso de UUIDs de 36 caracteres (descartando AUTO_INCREMENT). Históricamente, insertar GUIDs aleatorios provoca fragmentación de índices B-Tree (Page Splits) en MariaDB. Para evitar bloqueos, EF Core debe mapear explícitamente estas claves como CHAR(36) de ancho fijo mediante Fluent API.

### Cuándo usarlo en proyectos reales

En APIs de alto rendimiento. Generar los UUIDs en la capa de aplicación permite guardar registros en sistemas distribuidos, manejar concurrencia segura y conocer el ID de las entidades hijas antes de invocar await context.SaveChangesAsync().

### Buenas prácticas

- AsNoTracking: En endpoints HTTP GET invocados por Angular que solo leen datos, usa siempre .AsNoTracking(). Esto le dice a EF Core 5 que no rastree cambios, reduciendo el consumo de RAM a la mitad.
    
- Aislamiento: Mapear la base de datos mediante clases que implementen `IEntityTypeConfiguration<T>`.

### Errores comunes de juniors y cómo evitarlos

Error: Dejar que el ORM o MariaDB deduzca el tipo de la columna UUID, resultando en tipos genéricos VARCHAR(MAX) o fallos de collation.

Consecuencia: Degradación severa del rendimiento en los JOIN y fragmentación masiva en el motor InnoDB.

Solución: Especificar rígidamente HasColumnType("CHAR(36)") y ValueGeneratedNever().

### Ejemplo de código: Fluent API

  

```csharp
using Bazar.Domain.Entities;  
using Microsoft.EntityFrameworkCore;  
using Microsoft.EntityFrameworkCore.Metadata.Builders;  
  
namespace Bazar.Infrastructure.Persistence.Configuration  
{  
    // Se aísla la configuración de la BD lejos del Dominio  
    public class OrderConfiguration : IEntityTypeConfiguration<Order>  
    {  
        public void Configure(EntityTypeBuilder<Order> builder)  
        {  
            builder.ToTable("orders");  
  
            // Configuración estricta de UUID de 36 caracteres para MariaDB  
            builder.HasKey(x => x.Id);  
            builder.Property(x => x.Id)  
                .HasColumnType("CHAR(36)")  
                .IsRequired()  
                .ValueGeneratedNever(); // Desactivamos generación en BD (Auto_Increment)  
  
            builder.Property(x => x.CustomerId)  
                .HasColumnType("CHAR(36)")  
                .IsRequired();  
  
            builder.Property(x => x.TotalAmount)  
                .HasColumnType("DECIMAL(18,2)");  
        }  
    }  
}
```
