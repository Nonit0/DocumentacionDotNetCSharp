---
title: "MariaDB (Motor de BBDD)"
tags: [csharp, dotnet, database, mariadb, rdbms]
draft: false
---
# MariaDB (Motor Relacional)

- **Resumen:** Es un Sistema de Gestión de Bases de Datos Relacionales (RDBMS), nacido como un *[[fork]]* de código abierto de [[MySQL]].
- **Capa:** Capa de **Persistencia Física**. Es el software real que se ejecuta como un servicio en el servidor y almacena los datos en el disco duro.

### Análisis Técnico
- **Motor InnoDB:** Utiliza por defecto el motor de almacenamiento InnoDB, que garantiza el cumplimiento estricto de las propiedades **ACID** (Atomicidad, Consistencia, Aislamiento, Durabilidad), vitales para la integridad de un e-commerce.
- **Conexión en .NET:** En aplicaciones C#, el estándar para conectarse a este motor mediante Entity Framework Core es utilizar el paquete NuGet `Pomelo.EntityFrameworkCore.MySql`.
