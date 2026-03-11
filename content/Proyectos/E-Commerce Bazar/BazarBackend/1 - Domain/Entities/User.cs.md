---
title: "User"
tags: [csharp, dotnet, dotnet5, proyecto-bazar, domain, entity, security, identity]
draft: false
---
- **Resumen:** Representa a los usuarios del sistema, almacenando sus credenciales hasheadas y la información de sesión necesaria para JWT.
- **Capa y Responsabilidad:** Capa `Domain`. Almacena la identidad necesaria para la autorización en todo el sistema.

### Código Fuente
```csharp
public class User : BaseEntity
{
    public string Email { get; set; }
    public string PasswordHash { get; set; }
    public string RefreshToken { get; set; }
    public DateTime RefreshTokenExpiryTime { get; set; }
    public Guid RoleId { get; set; }
    public virtual Role Role { get; set; }
}
```

### Análisis Técnico
- **Seguridad JWT:** Incluye campos específicos para el *Refresh Token Pattern*, permitiendo sesiones largas sin comprometer la seguridad.
- **Relación con Roles:** Posee una clave foránea `RoleId` para determinar los permisos mediante la entidad `Role`.

### Buenas Prácticas Aplicadas
- **Shadow Properties / Seguridad:** No almacena la contraseña en texto plano (se utiliza BCrypt en la capa de Aplicación/API para generar el `PasswordHash`).
