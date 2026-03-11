---
title: "UsuariosController" 
tags: [csharp, dotnet, dotnet5, api, users] 
draft: false0
---
- **Resumen:** Provee información básica de los usuarios registrados y sus roles en el sistema.
- **Capa y Responsabilidad:** Capa **API**. Expone datos de perfil y administración de usuarios.
- **Análisis Técnico:**
    - **Proyecciones Linq:** Utiliza `.Select()` para devolver solo los campos estrictamente necesarios (`Id`, `Email`, `Rol`), protegiendo datos sensibles como contraseñas.
- **Buenas Prácticas Aplicadas:**
    - **Principio de Mínimo Privilegio:** No expone la entidad `User` completa.