---
## title: "AuthController"
tags: [csharp, dotnet, dotnet5, api, authentication, jwt] 
draft: false
---
- **Resumen:** Gestiona la autenticación de usuarios, incluyendo el inicio de sesión y la renovación de tokens (refresh token).
- **Capa y Responsabilidad:** Capa **API**. Expone los endpoints necesarios para la seguridad del sistema, comunicándose directamente con la infraestructura para validar credenciales.
- **Código Fuente:**
```csharp
[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    [HttpPost("login")]
    public async Task<ActionResult<AuthResponseDto>> Login(LoginDto loginDto) { ... }

    [HttpPost("refresh-token")]
    public async Task<ActionResult<AuthResponseDto>> RefreshToken(TokenApiModel tokenApiModel) { ... }

    private string GenerateAccessToken(User user) { ... }
    private string GenerateRefreshToken() { ... }
}
```
- **Análisis Técnico:**
    - **[[JWT_Fundamentos]] (JSON Web Tokens):** Utiliza `System.IdentityModel.Tokens.Jwt` para crear tokens firmados.
    - **[[BCrypt_Hashing]]:** Emplea `BCrypt.Net` para verificar los hashes de las contraseñas, asegurando que nunca se comparen en texto plano.
    - **Refresh Token Pattern:** Implementa una estrategia de refresco de tokens para mantener la sesión activa sin enviar credenciales constantemente.
- **Buenas Prácticas Aplicadas:**
    - **Validación de Claims:** Incluye roles y ID de usuario en los claims del token para autorizaciones rápidas en el backend.
    - **Seguridad:** Usa `RandomNumberGenerator` para crear refresh tokens criptográficamente fuertes.
