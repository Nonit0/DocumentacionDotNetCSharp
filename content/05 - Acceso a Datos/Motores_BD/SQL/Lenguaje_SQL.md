---
title: "Lenguaje SQL"
tags: [csharp, dotnet, database, sql, query-language]
draft: false
---
# Structured Query Language (SQL)



- **Resumen:** Es el lenguaje estándar de la industria diseñado para administrar, consultar y manipular datos en sistemas relacionales.
- **Concepto Clave:** SQL **no es un programa**, es un idioma. Diferentes motores ([[MariaDB]], [[SQL_Server]], [[PostgreSQL]]) "hablan" este idioma con ligeras variaciones (dialectos).

### Categorías del Lenguaje
- **DML (Data Manipulation Language):** `SELECT`, `INSERT`, `UPDATE`, `DELETE`.
- **DDL (Data Definition Language):** `CREATE`, `ALTER`, `DROP` (Usado internamente por las migraciones de Entity Framework).
- **DCL (Data Control Language):** `GRANT`, `REVOKE` (Permisos).

### Uso en nuestro stack
Normalmente, delegamos la escritura de SQL a **Entity Framework Core** o herramientas como **Dapper**. Sin embargo, conocer SQL puro es indispensable para optimizar consultas lentas o entender qué está haciendo el ORM por debajo.
