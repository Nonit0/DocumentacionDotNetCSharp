---
title: "BaseEntity" 
tags: [csharp, dotnet, domain, architecture, base-class] 
draft: false
---
- **Resumen:** Clase abstracta de la que heredan todas las entidades del sistema, proporcionando propiedades comunes como ID y auditoría básica.
- **Capa y Responsabilidad:** Capa **Domain**. Define el contrato base para cualquier entidad que deba ser persistida en el sistema.
- **Código Fuente:**
```csharp 
public abstract class BaseEntity
{
    public Guid Id { get; set; } = Guid.NewGuid();
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    public DateTime? UpdatedAt { get; set; }
    public bool IsDeleted { get; set; } = false;
}
```
- **Análisis Técnico:**
    - **UUID v4 (Guid):** Utiliza identificadores globales únicos en lugar de enteros autoincrementales, lo cual es ideal para sistemas distribuidos y previene la enumeración de recursos.
    - **Auditoría Básica:** Incluye `CreatedAt` y `UpdatedAt` para rastrear el ciclo de vida de los registros.
    - **Soft Delete:** Incorpora el flag `IsDeleted`, permitiendo borrados lógicos sin pérdida física de datos.
- **Buenas Prácticas Aplicadas:**
    - **DRY (Don't Repeat Yourself):** Evita declarar las propiedades de auditoría e ID en cada una de las entidades.