---
title: "Product"
tags: [csharp, dotnet, domain, entity, commerce] 
draft: false
---
- **Resumen:** Representa un producto individual dentro del catálogo del bazar, incluyendo precios, stock y relaciones con categorías e imágenes.
- **Capa y Responsabilidad:** Capa **Domain**. Es el corazón del negocio del e-commerce.
- **Código Fuente:**
```csharp
public class Product : BaseEntity
{
    public string Name { get; set; }
    public decimal Price { get; set; }
    public int StockQuantity { get; set; }
    public virtual Category Category { get; set; }
    public virtual ICollection<ProductImage> Images { get; set; }
}
```
- **Análisis Técnico:**
    - **Modificador `virtual`:** Habilita el **Lazy Loading** en EF Core, permitiendo que las propiedades de navegación se carguen solo cuando se acceda a ellas.
    - **Tipado Fuerte:** Usa `decimal` para el precio, garantizando precisión financiera.
- **Buenas Prácticas Aplicadas:**
    - **Relaciones Normalizadas:** En lugar de guardar URLs de imágenes en un string, utiliza una colección (`ProductImage`) para permitir múltiples perspectivas por producto.