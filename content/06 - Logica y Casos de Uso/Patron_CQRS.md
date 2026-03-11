---
title: "Patrón CQRS con MediatR"
tags: [csharp, dotnet]
draft: false
---
# CQRS (Command Query Responsibility Segregation)

Separamos las operaciones de lectura (Queries) de las de escritura (Commands) para escalar independientemente. Usamos **MediatR** como bus en memoria.

- **Command:** Modifica el estado (Insert, Update, Delete). No devuelve datos del dominio.
- **Query:** Solo lee estado. No modifica nada.
