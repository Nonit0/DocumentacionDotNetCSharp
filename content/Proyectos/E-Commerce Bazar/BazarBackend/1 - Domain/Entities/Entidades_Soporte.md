---
title: "Entidades de Soporte"
tags: [csharp, dotnet, dotnet5, proyecto-bazar, domain, entities, structural]
draft: false
---
# Entidades de Soporte

Resumen de otras entidades clave que dan soporte a la estructura del catálogo y las ventas:

- **`Category`:** Categorización del catálogo para filtrado. Mantiene una relación 1:N con `Product`.
- **`ProductImage`:** Soporta la normalización de imágenes. Permite que un producto tenga N fotos y define cuál es la principal mediante una bandera `IsPrimary`.
- **`OrderItem` / `TemporalOrderItem`:** Clases de asociación (Tablas intermedias) que rompen la relación N:N entre Pedidos y Productos. Almacenan información histórica vital como el precio en el momento exacto de la compra y la cantidad adquirida.
