---
title: "TemporalOrdersController"
tags: [csharp, dotnet, dotnet5, api, cart, persistence] 
draft: false
---
- **Resumen:** Gestiona el almacenamiento persistente del carrito de compras (TemporalOrders) asociado a un usuario autenticado.
- **Capa y Responsabilidad:** Capa **API**. Permite que el usuario recupere su carrito desde cualquier dispositivo tras el login.
- **Código Fuente:**
```csharp
[Authorize]
[ApiController]
[Route("api/[controller]")]
public class TemporalOrdersController : ControllerBase
{
    [HttpGet]
    public async Task<IActionResult> GetTemporalOrder() { ... }

    [HttpPost]
    public async Task<IActionResult> SaveTemporalOrder([FromBody] CreateTemporalOrderDto request) { ... }
}
```
- **Análisis Técnico:**
    - **Estrategia de Reemplazo:** Al guardar, borra versiones previas del carrito para asegurar que el estado persistido sea siempre el más actual (evita duplicados).
    - **Mapeo para Evitar Ciclos:** Usa proyecciones anónimas en el `Get` para evitar errores de serialización JSON por referencias circulares.
- **Buenas Prácticas Aplicadas:**
    - **Clean State:** Mantiene la lógica de "Estado Global del Carrito" sincronizada entre Frontend y Backend mediante este endpoint de respaldo.