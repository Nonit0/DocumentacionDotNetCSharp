---
title: "Reglas de Etiquetado .NET"
tags: [meta, reglas, obsidian]
draft: false
---

# ✅ Sistema de Etiquetado .NET — Versión Profesional y No Ambigua

Este sistema se basa en tres principios:
1. Clasificar siempre por tecnología base.
2. Indicar SIEMPRE la dirección temporal (introducido en, válido hasta, obsoleto en…).
3. Evitar tags que no indiquen claramente la relación con la versión.

## 🟩 1. Etiquetas base (siempre presentes)
Siempre que el contenido sea propio del ecosistema .NET:
`tags: [csharp, dotnet]`
Sirve para filtros globales como: "muéstrame TODO lo que es de C#" o ".NET".

## 🟦 2. Etiquetas por versión (no ambigua)
Para marcar cuando algo es específico de una versión: `dotnetX` (Ej: `dotnet5`, `dotnet6`).
Significa: *"Esto fue introducido, modificado o tiene comportamiento relevante en esta versión"*.

## 🟨 3. Novedades introducidas (feature)
Cuando algo aparece por primera vez en una versión concreta:
`tags: [csharp, dotnet, feature, dotnet6]`
Significado EXACTO: *"Esto apareció por primera vez en .NET 6"*.

## 🟥 4. Contenido obsoleto (deprecated)
Regla definitiva: `deprecated` SIEMPRE se acompaña de la ÚLTIMA VERSIÓN DONDE FUNCIONABA.
`tags: [csharp, deprecated, dotnet5]`
Significa literalmente: *"Esto funcionaba correctamente hasta .NET 5; queda obsoleto a partir de .NET 6"*.

---

## 🟪 5. Tabla resumen

| Caso | Tags correctos | Significado |
| :--- | :--- | :--- |
| Concepto general | `csharp, dotnet` | No depende de versiones |
| Novedad en .NET 6 | `csharp, dotnet, feature, dotnet6` | Nueva en esa versión |
| API solo en .NET 5 | `csharp, dotnet5` | Depende de esa versión |
| Obsoleto desde .NET 6 | `csharp, deprecated, dotnet5` | Última versión válida: .NET 5 |
| Cambia en .NET 8 | `csharp, dotnet8` | Cambia en esa versión |
