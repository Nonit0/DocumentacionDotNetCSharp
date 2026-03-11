---
title: "phpMyAdmin"
tags: [herramientas, database, phpmyadmin, ui, web-client]
draft: false
---
# phpMyAdmin

- **Tipo:** Cliente GUI basado en Web (escrito en PHP).
- **Resumen:** Herramienta visual clásica para interactuar con bases de datos MariaDB/MySQL directamente desde el navegador web.

### Análisis de la Herramienta
- **Ventaja (Fricción Cero):** Viene preinstalado y configurado de serie con paquetes como [[XAMPP]]. Simplemente abres `http://localhost/phpmyadmin` y tienes acceso completo a tus bases de datos locales.
- **Desventaja:** Al ejecutarse en un entorno web, suele tener problemas de rendimiento o bloqueos al intentar importar/exportar bases de datos masivas (dumps de varios gigabytes) debido a los límites de ejecución de PHP.
