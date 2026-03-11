---
title: "Inyección de Dependencias (DI)"
tags: [csharp, dotnet]
draft: false
---
# Ciclos de vida en Inyección de Dependencias

En el contenedor nativo de .NET tenemos 3 tiempos de vida principales:
- **Transient:** Una instancia nueva cada vez que se pide.
- **Scoped:** Una instancia por cada petición HTTP. (Ideal para DbContext).
- **Singleton:** Una única instancia para toda la vida de la aplicación.
