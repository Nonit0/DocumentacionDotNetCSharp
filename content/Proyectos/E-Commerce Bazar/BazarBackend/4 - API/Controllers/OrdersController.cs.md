---
title: "OrdersController" 
tags: [csharp, dotnet, dotnet5, api, orders, transactions] 
draft: false
---
- **Resumen:** Maneja la finalización de pedidos (checkout), convirtiendo un carrito temporal en una orden definitiva.
- **Capa y Responsabilidad:** Capa **API**. Gestiona el flujo crítico de compra y garantiza la integridad de los datos mediante transacciones.
- **Código Fuente:**
```csharp
[Route("api/[controller]")]
[ApiController]
[Authorize]
public class OrdersController : ControllerBase
{
    [HttpPost("checkout")]
    public async Task<IActionResult> Checkout() { ... }
}
```
- **Análisis Técnico:**
    - **Transacciones ACID:** Usa `BeginTransactionAsync()` para asegurar que si algo falla durante el checkout, se haga rollback completo (sin cargos fantasma).
    - **Seguridad JWT:** Extrae el `UserId` directamente del `ClaimTypes.NameIdentifier` del token, impidiendo suplantaciones por Body.
- **Buenas Prácticas Aplicadas:**
    - **Atomaticidad:** La operación de "crear orden" y "borrar carrito" sucede en una única unidad de trabajo.
    - **Protocolo HTTP:** Devuelve códigos de estado semánticos (200 OK, 401 Unauthorized, 500 Internal Error).