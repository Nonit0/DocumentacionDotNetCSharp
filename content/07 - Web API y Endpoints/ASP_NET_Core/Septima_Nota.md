---
title: "📝 Nota 01: Value types y Reference Types"
tags:
  - csharp9
  - dotnet5/api
  - basico
  - infraestructura
  - presentacion
draft: false
---
# Web API, Controladores Delgados y DTOs

### Qué es y para qué sirve

En una {{Arquitectura Limpia}}, los controladores de ASP.NET Core (`ControllerBase`) residen estrictamente en la capa externa de **Presentación**. Su única responsabilidad es actuar como mecanismo de entrega (Delivery Mechanism): traducir peticiones HTTP, pasarlas al núcleo del sistema (la capa de Aplicación/Casos de Uso) y transformar el resultado en respuestas HTTP estandarizadas (200 OK, 400 Bad Request, 201 Created).

### Cuándo usarlo en proyectos reales

Los controladores bien estructurados son esenciales para crear las APIs REST que Angular consumirá. Al mantener los controladores libres de reglas de negocio, puedes añadir en el futuro una interfaz gRPC o una Worker Service sin tener que reescribir la lógica de la aplicación.

### Buenas prácticas

- **Thin Controllers:** Un controlador _jamás_ debe hacer cálculos matemáticos de negocio, ni manipulaciones directas sobre la base de datos.
    
- **Aislamiento de Entidades:** Jamás debes exponer o devolver clases del Dominio directamente en los endpoints. Retorna siempre DTOs inmutables mapeados para Angular.
    

### Errores comunes de juniors y cómo evitarlos

**Error:** Inyectar el contexto de Entity Framework (`BazarDbContext`) directamente dentro del constructor del Controlador. **Consecuencia:** El endpoint de presentación queda brutalmente acoplado a un motor relacional específico, lo cual viola el Principio de Responsabilidad Única. Hace que el controlador sea extremadamente doloroso de someter a pruebas unitarias sin tener que falsificar la base de datos. **Solución:** Inyectar únicamente `IMediator` y delegar el peso a los manejadores de CQRS, logrando un desacoplamiento profundo.

### Ejemplo de código: Controlador Limpio.NET 5
```csharp
using MediatR;
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;
using Bazar.Application.Orders.Commands;

namespace Bazar.Api.Controllers
{
    [ApiController]
   ")]
    public class OrdersController : ControllerBase
    {
        private readonly IMediator _mediator;

        // Inyección única. El controlador es completamente ciego frente a la BD.
        public OrdersController(IMediator mediator)
        {
            _mediator = mediator;
        }

        [HttpPost]
        public async Task<IActionResult> CreateOrder( CreateOrderCommand command)
        {
            // El controlador delega la ejecución al manejador CQRS.
            var orderId = await _mediator.Send(command);
            
            // Retorna convención REST: 201 Created y la cabecera Location hacia el recurso
            return CreatedAtAction(nameof(GetOrderById), new { id = orderId }, new { Id = orderId });
        }

        [HttpGet("{id}")]
        public IActionResult GetOrderById(string id)
        {
            // El código real ejecutaría una Query (ej. GetOrderByIdQuery)
            return Ok();
        }
    }
}
```