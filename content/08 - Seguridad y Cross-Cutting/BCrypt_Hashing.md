---
title: "BCrypt.Net-Next"
tags: [csharp, dotnet, security, hashing]
draft: false
---
# BCrypt.Net-Next

- **Resumen:** Librería especializada en el hashing de contraseñas mediante un algoritmo de "coste variable" que protege contra ataques de fuerza bruta.
- **Capa y Responsabilidad:** Cross-cutting Concern (Seguridad). Aunque normalmente se llama desde un servicio de autenticación o controlador (Auth), su responsabilidad es garantizar que las contraseñas nunca se almacenen en texto plano en la BBDD.

### Análisis Técnico
- Utiliza la función `BCrypt.HashPassword` para crear el hash y `BCrypt.Verify` para validarlo durante el proceso de login.
- Implementa automáticamente un **"Salt"** (sal) aleatorio adjunto a la contraseña antes del hashing, lo que evita ataques basados en tablas arcoíris (Rainbow Tables).

### Buenas Prácticas Aplicadas
- **One-Way Hashing:** Las contraseñas no se cifran (no se pueden descifrar para ver la original), se *hashean* (es un proceso irreversible). Este es el estándar de seguridad industrial más alto para credenciales.
- **Work Factor (Coste):** Permite ajustar el tiempo de cómputo del algoritmo para ralentizar los ataques de fuerza bruta, adaptándose a las mejoras futuras en la potencia del hardware.
