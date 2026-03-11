---
title: "Manejo Global de Excepciones (IExceptionHandler)"
tags: [csharp, dotnet, feature, dotnet8]
draft: false
---
# IExceptionHandler en .NET 8

.NET 8 introdujo una forma nativa y elegante de manejar excepciones globalmente sin necesidad de crear Middlewares personalizados complejos.

```csharp
public class GlobalExceptionHandler : IExceptionHandler
{
    public async ValueTask<bool> TryHandleAsync(HttpContext httpContext, Exception exception, CancellationToken cancellationToken)
    {
        // Lógica para loggear y devolver ProblemDetails
        return true;
    }
}
```
