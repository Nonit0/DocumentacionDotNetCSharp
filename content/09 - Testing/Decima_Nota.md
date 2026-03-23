---
title: "📝 Nota 10: Patron AAA"
tags:
  - csharp9
  - testing
  - dotnet5/patrones
  - patron-aaa
  - performance
  - xunit
draft: false
---
# Testing: El Patrón AAA (Arrange, Act, Assert)

### Qué es y para qué sirve

El patrón AAA (Arrange, Act, Assert) es el estándar de la industria para estructurar pruebas unitarias de forma limpia y legible. Divide cada test estrictamente en tres fases:

1. **Arrange (Preparar):** Configuras el estado inicial. Instancias objetos, declaras variables y configuras los _Mocks_ (simulaciones) de las dependencias.
    
2. **Act (Actuar):** Ejecutas exactamente la función o método específico que quieres probar. Debe ser, por lo general, una sola línea de código.
    
3. **Assert (Afirmar):** Verificas que el resultado o el estado final es el esperado.
    

### Cuándo usarlo en proyectos reales

En **todas** tus pruebas unitarias y de integración utilizando frameworks como `xUnit` en.NET 5. Garantiza que cualquier desarrollador que lea la prueba entienda inmediatamente qué se está configurando, qué se ejecuta y qué se espera.

### Buenas prácticas

- **Nomenclatura descriptiva:** Nombra tus métodos de prueba usando el patrón `Metodo_Estado_ResultadoEsperado` (ej. `PlaceOrder_WithEmptyCart_ThrowsException`).
    
- **Cero Lógica:** Un test jamás debe contener sentencias `if`, `switch`, `for` o `while`. Si necesitas iterar, debes usar el atributo `con` que provee `xUnit` para inyectar múltiples casos de prueba al mismo método.
    
- **Un solo Act:** Si tu test tiene múltiples "Acts", estás probando demasiadas cosas a la vez. Divídelo en varias pruebas.
    

### Errores comunes de juniors y cómo evitarlos

**Error:** Escribir pruebas largas, sin separación visual, mezclando aserciones con configuraciones, o haciendo un "Mock" de absolutamente todo (incluso de entidades de dominio). **Consecuencia:** Tests frágiles ("Fragile Tests") que se rompen con cualquier mínimo cambio en el código, y que son imposibles de leer cuando fallan en el pipeline de CI/CD. **Solución:** Respetar los comentarios `// Arrange`, `// Act`, `// Assert` como separadores físicos en el código y usar instancias reales para objetos simples (como DTOs o Value Objects), reservando los Mocks (con librerías como `Moq`) exclusivamente para las inyecciones de puertos de infraestructura (ej. `IUserRepository`).

### Ejemplo de código: Test Limpio en.NET 5 con xUnit y Moq
```csharp
using System;
using System.Threading.Tasks;
using Moq;
using Xunit;
using Bazar.Application.Services;
using Bazar.Domain.Entities;
using Bazar.Domain.Interfaces;

namespace Bazar.Tests.Application
{
    public class OrderServiceTests
    {
        [Fact]
        public async Task PlaceOrder_WhenCustomerDoesNotExist_ReturnsFailureResult()
        {
            // Arrange
            var customerId = "550e8400-e29b-41d4-a716-446655440000"; // UUID válido
            var mockRepo = new Mock<IUserRepository>();
            
            // Simulamos que la base de datos no encuentra al usuario
            mockRepo.Setup(repo => repo.GetByIdAsync(customerId))
                   .ReturnsAsync((User)null);

            var sut = new OrderService(mockRepo.Object); // SUT: System Under Test

            // Act
            var result = await sut.PlaceOrderAsync(customerId, new List<OrderItemDto>());

            // Assert
            Assert.False(result.Success);
            Assert.Equal("Usuario no encontrado.", result.ErrorMessage);
            
            // Verificamos que el servicio intentó buscar al usuario exactamente 1 vez
            mockRepo.Verify(r => r.GetByIdAsync(customerId), Times.Once); 
        }
    }
}
```