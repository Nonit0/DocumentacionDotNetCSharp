---
title: "📝 Nota 1: 01 - C# Basico / Primera_Nota"
tags: [csharp9, dotnet5/memoria, performance, basico]
draft: false
---
# Value Types vs Reference Types en C# 9

### Qué es y para qué sirve

En lenguajes interpretados como Python, todo es un objeto alojado dinámicamente. En C# (.NET 5), la memoria se divide drásticamente en dos zonas: el Stack (Pila) y el Managed Heap (Montículo Gestionado).

- Reference Types (class): Se asignan en el Heap. El Garbage Collector (GC) debe rastrearlos y limpiarlos.
    
- Value Types (struct): Se asignan en el Stack (o inline dentro de otros objetos). Son efímeros, se destruyen instantáneamente al salir del ámbito de la función y no presionan al GC.
    
- Records (record - Nuevo en C# 9): Son Reference Types inmutables por defecto, que proporcionan semántica de igualdad basada en valores.
    

### Cuándo usarlo en proyectos reales

- Usa class para tus [[Entidades de Dominio]] (ej. Order, Product) que tienen identidad y ciclo de vida.
    
- Usa struct (específicamente readonly struct) para datos transitorios pequeños de alta concurrencia o Value Objects en [[Domain-Driven Design]].
    
- Usa record estrictamente para tus DTOs (Data Transfer Objects) en la comunicación entre tu API.NET 5 y tu frontend Angular.
    

### Buenas prácticas

Evita la "obsesión por los primitivos". En lugar de pasar un simple string para un ID, encapsúlalo en un readonly struct. Esto evita que el recolector de basura se sobrecargue en sistemas masivos.

### Errores comunes de juniors y cómo evitarlos

Error: Crear clases (class) gigantes para transferir datos a Angular, y modificarlas en múltiples capas.

Consecuencia: Fugas de memoria, recolecciones de basura del Heap (generación 2) que pausan el hilo de Kestrel (servidor web de.NET).

Solución: Usar record de C# 9 para DTOs.

### Ejemplo de código: Implementación en.NET 5

Uso de un Value Object para encapsular la regla de UUID de 36 caracteres exigida por la base de datos [[MariaDB]].

```Csharp 
using System;

namespace Bazar.Domain.Common
{
    // readonly struct para alojarlo en el Stack (0 impacto en el GC)
    public readonly struct EntityId : IEquatable<EntityId>
    {
        public string Value { get; }

        private EntityId(string value) => Value = value;

        // Implementación para mantener invariantes de 36 caracteres exactos en BD
        public static EntityId Generate()
        {
            return new EntityId(Guid.NewGuid().ToString("D")); 
        }

        public bool Equals(EntityId other) => Value == other.Value;
        public override bool Equals(object obj) => obj is EntityId other && Equals(other);
        public override int GetHashCode() => Value.GetHashCode();
        public static bool operator ==(EntityId left, EntityId right) => left.Equals(right);
        public static bool operator!=(EntityId left, EntityId right) =>!left.Equals(right);
    }

    // C# 9 Record: DTOs inmutables para Angular
    public record ProductDto(string Id, string Name, decimal Price);
}
```
