---
title: "JSON Web Tokens (JWT)"
tags: [csharp, dotnet, seguridad, jwt]
draft: false
---
# JSON Web Tokens (JWT)

- **Resumen:** Estándar abierto (RFC 7519) que define una forma compacta y autónoma de transmitir información de forma segura entre las partes como un objeto JSON.
- **Capa y Responsabilidad:** Cross-cutting Concern (Seguridad). Se configura en la capa API (Startup.cs) y se consume en los Controladores mediante el atributo `[Authorize]`.

### Análisis Técnico
- **Estructura:** Compuesta por Header, Payload (Claims como el ID de usuario y Rol) y Signature.
- **Algoritmo:** Utiliza HMAC SHA256 para firmar los tokens usando una clave secreta.
- **Stateless:** La API no guarda el estado de la sesión en memoria; toda la información necesaria para identificar al usuario "viaja" dentro del token.

### Buenas Prácticas Aplicadas
- **Expiración de Corto Plazo:** Los tokens de acceso tienen una vida limitada para minimizar el daño en caso de robo.
- **Validación Estricta:** El sistema valida emisor (Issuer), audiencia (Audience), firma y tiempo de vida en cada petición.
