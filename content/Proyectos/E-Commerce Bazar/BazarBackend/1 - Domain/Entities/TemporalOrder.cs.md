---
title: "TemporalOrder (Carrito)"
tags: [csharp, dotnet, dotnet5, proyecto-bazar, domain, entity, cart, persistence]
draft: false
---
### Carrito

- **Resumen:** Entidad diseñada para persistir el carrito de compras en la base de datos, permitiendo "empezar la compra en el móvil y terminarla en el PC".
- **Capa y Responsabilidad:** Capa `Domain`. Gestiona el estado previo a la venta.
- **Código Fuente:**
```csharp 
public class TemporalOrder
{

	public Guid Id { get; set; } = Guid.NewGuid();
	
	// Relación 1 a 1 con el Usuario (Un usuario solo tiene 1 carrito activo)
	public Guid UserId { get; set; }
	public User User { get; set; } = null!;
	
	// Auditoría básica
	public DateTime LastUpdated { get; set; } = DateTime.UtcNow;
	  
	// Relación 1 a N con los items
	public ICollection<TemporalOrderItem> Items { get; set; } = new List<TemporalOrderItem>();
}
```

> **Diferencia de Diseño (Ojo de Arquitecto):** Curiosamente, esta entidad **no hereda** de `BaseEntity` directamente en su código actual, aunque mantiene su propio `Id` y lógica de auditoría propia (`LastUpdated`). Esto subraya su naturaleza efímera o "temporal" frente al resto del catálogo estático.

### Análisis Técnico
- **Persistencia de Sesión:** A diferencia de un carrito en `LocalStorage` (Frontend), este sobrevive al cierre del navegador o cambio de dispositivo porque vive en [[MariaDB]]/[[SQL]].
