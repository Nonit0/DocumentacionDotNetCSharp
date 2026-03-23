---
title: "📝 Nota 11: Global Exception handler"
tags:
  - csharp9
  - dotnet5/api
  - basico
  - arquitectura
  - middleware
  - excepciones
  - performance
  - arquitectura/clean
draft: false
---
# Manejo Global de Excepciones (Middleware)

### Qué es y para qué sirve

En aplicaciones web, los errores inesperados (como la caída de la base de datos o un fallo de referencia nula) lanzan excepciones. Si estas no se controlan, la API de ASP.NET Core devolverá una respuesta HTML con el _Stack Trace_ (la traza de la pila) completo. El **Manejo Global de Excepciones** intercepta de forma centralizada cualquier error que ocurra durante el ciclo de vida de la petición HTTP, registra el error de forma estructurada y devuelve una respuesta estándar (generalmente en formato JSON y siguiendo el estándar `ProblemDetails`) que el cliente Angular puede procesar sin romper su interfaz.

### Cuándo usarlo en proyectos reales

**Siempre**. Es una de las primeras piezas de infraestructura que un Senior configura en un proyecto nuevo. El cliente (Angular) nunca debe recibir una página HTML de error del servidor 500, sino una respuesta JSON uniforme que el interceptor de Angular (que hicimos en la nota anterior) pueda parsear fácilmente.

### Buenas prácticas

- Crea una clase `Middleware` personalizada en lugar de llenar todos tus controladores con bloques `try-catch`.
    
- Crea excepciones de Dominio (ej. `ValidationException`, `NotFoundException`). El middleware debe capturarlas y mapearlas a códigos HTTP semánticos (400 BadRequest, 404 NotFound). Cualquier otra excepción estándar de C# se trata como un fallo crítico (500 Internal Server Error) y se ocultan los detalles al cliente por seguridad.
    

### Errores comunes de juniors y cómo evitarlos

**Error:** Rodear cada método del controlador y del servicio con `try {... } catch (Exception ex) { return BadRequest(ex.Message); }`. **Consecuencia:** Código repetitivo ("Boilerplate"), alta carga cognitiva, y lo más peligroso: filtración de información sensible de la infraestructura (rutas de servidor, nombres de tablas) hacia el cliente, facilitando ataques. **Solución:** Delegar el manejo al _Pipeline_ de.NET 5. Los servicios y controladores deben estar "limpios" de lógica de captura de errores, a menos que sea un error del que se puedan recuperar de inmediato.

### Ejemplo de código: Middleware de Excepciones en.NET 5
```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using System.Net;
using System.Text.Json;
using System.Threading.Tasks;
using Bazar.Domain.Exceptions; // Tus excepciones personalizadas

namespace Bazar.Api.Middleware
{
    public class GlobalExceptionMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<GlobalExceptionMiddleware> _logger;

        public GlobalExceptionMiddleware(RequestDelegate next, ILogger<GlobalExceptionMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            try
            {
                // Dejamos que la petición fluya hacia los controladores
                await _next(context);
            }
            catch (Exception ex)
            {
                // Si algo falla en cualquier capa, lo interceptamos aquí
                _logger.LogError(ex, "Ha ocurrido un error no controlado.");
                await HandleExceptionAsync(context, ex);
            }
        }

        private static Task HandleExceptionAsync(HttpContext context, Exception exception)
        {
            context.Response.ContentType = "application/json";

            // Mapeo de Excepciones de Dominio a HTTP Status Codes
            context.Response.StatusCode = exception switch
            {
                NotFoundException => (int)HttpStatusCode.NotFound,
                ValidationException => (int)HttpStatusCode.BadRequest,
                UnauthorizedAccessException => (int)HttpStatusCode.Unauthorized,
                _ => (int)HttpStatusCode.InternalServerError // Por defecto: 500
            };

            // Creamos una respuesta estándar sin revelar el Stack Trace real en producción
            var response = new 
            {
                StatusCode = context.Response.StatusCode,
                Message = context.Response.StatusCode == 500? "Error interno del servidor." : exception.Message,
                Detailed = exception is ValidationException valEx? valEx.Errors : null
            };

            var jsonResponse = JsonSerializer.Serialize(response, new JsonSerializerOptions { PropertyNamingPolicy = JsonNamingPolicy.CamelCase });
            
            return context.Response.WriteAsync(jsonResponse);
        }
    }
}

// En Startup.cs (.NET 5), se registra al inicio del pipeline:
// public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
// {
//     app.UseMiddleware<GlobalExceptionMiddleware>();
//    ...
// }
```