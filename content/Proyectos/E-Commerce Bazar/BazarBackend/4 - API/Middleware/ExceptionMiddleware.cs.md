---
title: "ExceptionMiddleware" 
tags: [csharp, dotnet, dotnet5, api, middleware, error-handling] 
draft: false
---
- **Resumen:** Capturador global de excepciones que asegura que todas las respuestas de error tengan un formato JSON estándar.
- **Capa y Responsabilidad:** Capa **API**. Actúa como la primera línea de defensa del pipeline de ASP.NET Core.
- **Código Fuente**
```csharp
public async Task InvokeAsync(HttpContext context)
{
    try { await _next(context); }
    catch (Exception ex)
    {
        _logger.LogError(ex, ex.Message);
        // ... Serialización de Error ...
    }
}
```
- **Análisis Técnico:**
    - **Punto de Inyección:** Se registra en [[Startup.cs]] como el primer middleware (`app.UseMiddleware<ExceptionMiddleware>()`).
    - **Diferenciación de Entorno:** Incluye el StackTrace solo si el entorno es `Development`, evitando fugas de información sensible en producción.
- **Buenas Prácticas Aplicadas:**
    - **Consistencia:** Garantiza que el Frontend siempre reciba un objeto `ApiException` ante cualquier fallo inesperado, facilitando el manejo de errores en el cliente.