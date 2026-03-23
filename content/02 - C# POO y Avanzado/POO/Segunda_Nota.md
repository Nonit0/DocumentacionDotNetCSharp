---
title: "📝 Nota 2: Interfaces y Clases Abstractas"
tags:
  - csharp9
  - dotnet5/memoria
  - arquitectura/hexagonal
  - arquitectura/clean
  - solid/dip
draft: false
aliases:
  - Abstracción
  - Clean Architecture
  - Arquitectura Hexagonal
  - Puertos y Adaptadores
---
# Interfaces y Abstracción en Domain-Driven Design

### Qué es y para qué sirve

La Arquitectura Hexagonal basa todo su poder en el Principio de Inversión de Dependencias (DIP de SOLID). En una arquitectura tradicional, el Dominio depende de la Base de Datos. En DDD con Hexagonal, el Dominio es el centro absoluto y no sabe que [[MariaDB]] existe.

Para lograr esto, el Dominio define Interfaces (Puertos) que declaran qué necesita el negocio (ej. IProductRepository). La capa de Infraestructura implementa esas interfaces (Adaptadores).

### Cuándo usarlo en proyectos reales

Siempre que construyas aplicaciones con Angular +.NET 5 donde las reglas de negocio sean críticas (ej. proyecto E-Commerce Bazar). Si mañana cambias MariaDB por PostgreSQL, tu capa de Dominio (la más valiosa) no se modifica en absoluto.

### Buenas prácticas

- Entidades Ricas vs Anémicas: Las entidades no deben tener set públicos. En C# 9, si debes permitir inicialización rápida usa init, pero idealmente usa constructores privados y métodos que representen acciones de negocio (ej. AgregarItem).
    
- La lógica de acceso a datos (using Microsoft.EntityFrameworkCore) nunca debe cruzar hacia la capa 1 - Domain.
    

### Errores comunes de juniors y cómo evitarlos

Error: Poner anotaciones de base de datos (``, [Key]) directamente en las clases de Dominio.

Consecuencia: Tu dominio se acopla a [[Entity Framework]]. Si el ORM cambia, tu negocio se rompe.

Solución: Usar Fluent API (IEntityTypeConfiguration) exclusivamente en la Infraestructura.

### Ejemplo de código: Puerto y Entidad Rica
```csharp
using System;  
using System.Collections.Generic;  
using Bazar.Domain.Common;  
  
namespace Bazar.Domain.Entities  
{  
    // Entidad Rica - Protege sus invariantes  
    public class Order : BaseEntity  
    {  
        public string CustomerId { get; private set; }  
        public decimal TotalAmount { get; private set; }  
         
        // Mantenemos la colección protegida  
        private readonly List<string> _productIds = new();  
        public IReadOnlyCollection<string> ProductIds => _productIds.AsReadOnly();  
  
        private Order(string customerId)  
        {  
            Id = EntityId.Generate().Value; // UUID de 36 caracteres generado en el dominio  
            CustomerId = customerId;  
        }  
  
        // Método de fábrica explícito  
        public static Order Create(string customerId)  
        {  
            if (string.IsNullOrWhiteSpace(customerId))  
                throw new ArgumentException("Cliente inválido.");  
            return new Order(customerId);  
        }  
  
        // Intención de negocio  
        public void AddProduct(string productId, decimal price)  
        {  
            _productIds.Add(productId);  
            TotalAmount += price;  
        }  
    }  
}  
  
namespace Bazar.Domain.Interfaces  
{  
    // PUERTO: El dominio dicta el contrato.  
    public interface IOrderRepository  
    {  
        Task AddAsync(Order order);  
    }  
}
```
