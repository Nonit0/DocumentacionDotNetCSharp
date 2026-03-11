---
title: "Records vs Clases"
tags: [csharp, dotnet, feature, dotnet5]
draft: false
---
# Records vs Clases en C#

Los `record` son tipos de referencia inmutables por defecto, ideales para DTOs y objetos de valor. Tienen igualdad basada en valor, no en referencia.

```csharp
// Novedad desde .NET 5
public record UsuarioDto(Guid Id, string Nombre, string Email);
```
