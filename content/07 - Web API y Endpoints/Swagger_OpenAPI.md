---
title: "Swagger (OpenAPI 3.0)"
tags: [csharp, dotnet, api, documentation, swagger]
draft: false
---
# Swagger (OpenAPI 3.0)

- **Resumen:** Herramienta de documentación interactiva que permite visualizar y probar los endpoints de la API en tiempo real desde el navegador.
- **Capa y Responsabilidad:** Capa API (Presentación). Su configuración reside en [[Startup.cs]] (o [[Program.cs]]) y su ejecución es un middleware que suele activarse solo en entornos de desarrollo (`IsDevelopment`).

### Análisis Técnico
- Se implementa comúnmente en .NET mediante el paquete **Swashbuckle.AspNetCore**.
- Genera automáticamente un archivo `swagger.json` basado en la reflexión de los controladores y sus atributos (`[HttpGet]`, `[HttpPost]`, etc.).

### Buenas Prácticas Aplicadas
- **Self-Documentation:** La API se documenta a sí misma directamente desde el código fuente, evitando que la documentación quede desactualizada.
- **Sandbox:** Facilita a los desarrolladores de Frontend (ej. Angular, React) probar la respuesta y el formato de los endpoints sin necesidad de usar herramientas externas como Postman.
