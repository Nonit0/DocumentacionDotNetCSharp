---
title: "Medir tiempo de ejecución"
tags: [csharp, dotnet]
draft: false
---
# Snippet: Stopwatch

```csharp
var sw = System.Diagnostics.Stopwatch.StartNew();
// Tu código pesado aquí
sw.Stop();
Console.WriteLine($"Tiempo: {sw.ElapsedMilliseconds} ms");
```
