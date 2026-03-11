---
title: "Lazy Loading (Propiedades Virtuales)"
tags: [csharp, dotnet, ef-core, orm, optimization]
draft: false
---
# Lazy Loading (Carga Diferida)

- **Resumen:** Técnica de optimización que retrasa la carga de objetos relacionados en base de datos hasta que son explícitamente requeridos por el código.
- **Capa y Responsabilidad:** Configuración del **Acceso a Datos** (Entity Framework), pero que requiere preparar las entidades en la capa de **Dominio** haciéndolas heredables.

### Uso en el Proyecto (Análisis Técnico)
- Se observa en el uso de la palabra clave `virtual` en las propiedades de navegación de las entidades (ej: `public virtual Category Category { get; set; }`).
- **¿Cómo funciona la magia?** Cuando EF Core materializa estas entidades desde la base de datos, genera dinámicamente clases proxy (en tiempo de ejecución) que heredan de tu entidad y sobrescriben esa propiedad `virtual`. Al intentar acceder a la propiedad por primera vez, el proxy intercepta la llamada y lanza la consulta SQL para traer esos datos.



### Buenas Prácticas y Advertencias (Ojo de Arquitecto)
- **Ventaja (Optimización de memoria):** Evita traer grafos de objetos gigantes a la memoria de C# si no los vas a usar. Por ejemplo, si solo consultas el precio de un producto, no te traes toda la información de su categoría.
- **Peligro (El problema N+1):** En una Web API, si devuelves una lista de 100 productos y tu serializador JSON intenta leer la propiedad `Category` de cada uno, EF Core lanzará 1 consulta inicial + 100 consultas individuales para las categorías. En estos casos, suele ser mejor usar **Eager Loading** explícito con `.Include()` en tu capa de Aplicación/Repositorio.
