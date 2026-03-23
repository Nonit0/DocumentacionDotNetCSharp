---
title: "📝 Nota 04: Seguridad Microsoft"
tags:
  - csharp9
  - dotnet5/seguridad
  - performance
  - basico
  - seguridad
  - identity
  - jwt
draft: false
---
# Seguridad Moderna: Identity y JWT en.NET 5

### Qué es y para qué sirve

ASP.NET Core Identity es el ecosistema nativo para gestionar membresías. Por defecto, acopla la autenticación a cookies. En arquitecturas modernas con frontend SPA en Angular, se disocia este comportamiento emitiendo JSON Web Tokens ({{JWT}}).

Identity verifica credenciales de forma segura (con hashes como PBKDF2), y la API genera un token firmado que Angular adjuntará vía un HttpInterceptor.

### Cuándo usarlo en proyectos reales

Obligatorio para cualquier SPA empresarial. Además de la autenticación, Identity proporciona herramientas para bloqueo por fuerza bruta, confirmación de correos y manejo de reclamaciones (Claims).

### Buenas prácticas

En.NET 5, debes extender `IdentityUser<string>` para garantizar que las tablas de seguridad de Microsoft (ej. AspNetUsers) cumplan la regla estricta de claves primarias UUID de 36 caracteres. Los tokens JWT deben tener vidas cortas (ej. 15 minutos).

### Errores comunes de juniors y cómo evitarlos

Error: Almacenar la clave secreta del JWT directamente en el código fuente o usar claves muy cortas.

Consecuencia: Vulnerabilidad crítica. Un atacante puede falsificar JWTs con rol de "Admin" y vulnerar todo el sistema.

Solución: Usar algoritmos HMAC-SHA256 con claves de más de 256 bits inyectadas mediante variables de entorno en Startup.cs.

### Ejemplo de código: IdentityUser Personalizado
```csharp
using Microsoft.AspNetCore.Identity;  
using System;  
  
namespace Bazar.Infrastructure.Identity  
{  
    // Adaptamos el usuario de Identity para cumplir con la arquitectura: ID = CHAR(36)  
    public class ApplicationUser : IdentityUser<string>  
    {  
        public string FirstName { get; set; }  
        public string LastName { get; set; }  
  
        public ApplicationUser()  
        {  
            // Forzamos el UUID de 36 caracteres estricto al instanciar  
            Id = Guid.NewGuid().ToString("D");  
        }  
    }  
}  
  
// Extracto de configuración en Startup.cs (.NET 5):  
/*  
services.AddIdentity<ApplicationUser, IdentityRole<string>>()  
    .AddEntityFrameworkStores<IdentityContext>()  
    .AddDefaultTokenProviders();  
*/
```
