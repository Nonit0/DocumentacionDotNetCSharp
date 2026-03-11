---
title: "LINQ (Language Integrated Query)"
tags: [csharp, dotnet, linq]
draft: false
---
# LINQ (Language Integrated Query)

- **Resumen:** Conjunto de capacidades de C# que permite realizar consultas sobre diferentes fuentes de datos (colecciones, bases de datos) utilizando una sintaxis uniforme.
- **Capa y Responsabilidad:** Fundamental para transformar Entidades en DTOs y realizar filtrados eficientes en memoria o contra base de datos.

### Análisis Técnico
- **Proyecciones:** Utiliza `.Select()` para transformar tipos complejos en objetos más sencillos, optimizando lo que se devuelve al cliente.
- **Deferred Execution:** Las consultas no se ejecutan hasta que se recorren (ej. un `foreach`) o se llaman a métodos materializadores como `.ToList()` o `.ToArray()`, lo que mejora el rendimiento.
- **Integración con EF Core:** Traduce las expresiones lambda directamente a sentencias SQL (ej. `WHERE`, `ORDER BY`).

### Buenas Prácticas Aplicadas
- **Proyecciones Limpias:** Evita traer todos los datos de la tabla. Usar `.Select()` asegura que solo viajan por la red las propiedades estrictamente necesarias para los DTOs.
- **Legibilidad:** Transforma bucles complejos y condicionales anidados en cadenas de métodos declarativos muy fáciles de leer y mantener.
