---
title: "Order"
tags: [csharp, dotnet, dotnet5, proyecto-bazar, domain, entity, commerce, sales]
draft: false
---
- **Resumen:** Representa una venta finalizada y confirmada por un usuario.
- **Capa y Responsabilidad:** Capa `Domain`. Documento legal/comercial de la transacción.

### Código Fuente
```csharp
public class Order : BaseEntity
{
    public Guid UserId { get; set; }
    public virtual User User { get; set; }
    public DateTime OrderDate { get; set; } = DateTime.UtcNow;
    public decimal TotalAmount { get; set; }
    public string Status { get; set; } = "Cart"; // "Cart", "Processing", "Delivered"...
    public virtual ICollection<OrderItem> Items { get; set; }
}
```

### Análisis Técnico
- **Snapshot de Datos:** El `TotalAmount` se calcula en el momento de la orden para evitar inconsistencias si los precios de los productos cambian en el futuro.
