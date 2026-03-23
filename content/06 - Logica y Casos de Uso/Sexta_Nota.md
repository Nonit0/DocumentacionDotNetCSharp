---
title: "📝 Nota 06: Patron CQRS"
tags:
  - csharp9
  - dotnet5/seguridad
  - basico
  - arquitectura/patrones
  - casos-de-uso
  - cqrs
  - mediatr
draft: false
---
# Patrón CQRS (Segregación de Responsabilidades)

### Qué es y para qué sirve

CQRS (Command Query Responsibility Segregation) es un patrón arquitectónico que separa estrictamente las operaciones que mutan el estado del sistema (Comandos) de las que solo recuperan datos (Consultas/Queries). En aplicaciones.NET 5 empresariales, esto se orquesta habitualmente mediante la librería `MediatR`. En lugar de tener "Servicios Gordos" (ej. `OrderService`) con docenas de dependencias y miles de líneas, cada flujo de negocio se aísla en su propio manejador independiente (ej. `CreateOrderCommandHandler`).

### Cuándo usarlo en proyectos reales

En sistemas empresariales como {{E-Commerce Bazar}}, las exigencias de lectura y escritura son altamente asimétricas. Por ejemplo, leer el catálogo de productos requiere alta velocidad (potencialmente usando {{Dapper}} o Redis), mientras que procesar un pago y crear la orden requiere la protección transaccional estricta del ORM ({{Entity Framework}}).

### Buenas prácticas

- **Inmutabilidad:** Los Commands y Queries deben ser objetos de solo lectura. Aprovecha los `record` introducidos en C# 9 para definirlos.
    
- **Validaciones Centralizadas:** Implementa validaciones (ej. con FluentValidation) utilizando el _Pipeline Behavior_ de MediatR para rechazar datos sucios antes de que siquiera toquen el manejador de la lógica de negocio.
    

### Errores comunes de juniors y cómo evitarlos

**Error:** Aplicar CQRS en aplicaciones CRUD genéricas y sencillas (sobreingeniería extrema) o reutilizar el mismo modelo de datos que se usa de entrada para devolvérselo a Angular como salida. **Consecuencia:** Fragmentación innecesaria del código que dispara los tiempos de desarrollo sin proporcionar ventajas reales de rendimiento o escalabilidad. **Solución:** CQRS brilla cuando hay lógica de dominio compleja. Si un módulo es solo un "Guardar" y "Mostrar" básico a una tabla, un patrón de Servicio simple es más profesional.

### Ejemplo de código: Implementación en.NET 5
```csharp
using MediatR;
using System.Threading;
using System.Threading.Tasks;
using Bazar.Domain.Entities;
using Bazar.Domain.Interfaces;

namespace Bazar.Application.Orders.Commands
{
    // 1. El Comando: Definido como record inmutable de C# 9
    public record CreateOrderCommand(string CustomerId, decimal InitialAmount) : IRequest<string>;

    // 2. El Manejador: Completamente asilado y solo inyecta lo que necesita
    public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, string>
    {
        private readonly IOrderRepository _repository;

        public CreateOrderCommandHandler(IOrderRepository repository)
        {
            _repository = repository;
        }

        public async Task<string> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
        {
            // La API y la Infraestructura desconocen cómo ocurre esto
            var order = Order.Create(request.CustomerId);
            
            await _repository.AddAsync(order);
            
            return order.Id; // Se retorna el ID generado en la capa de dominio
        }
    }
}
```