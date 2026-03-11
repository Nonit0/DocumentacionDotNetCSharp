---
title: "Minimal APIs"
tags: [csharp, dotnet, feature, dotnet6]
draft: false
---
# Minimal APIs

Introducidas en .NET 6, eliminan la necesidad de crear Clases Controlador pesadas, reduciendo el *boilerplate*.

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/api/usuarios", () => Results.Ok(new { Mensaje = "Hola Mundo" }));

app.Run();
```
