---
title: "Entidad Base Auditable"
tags: [csharp, dotnet]
draft: false
---
# Patrón: Entidad Base Auditable

Todas nuestras tablas en base de datos deberían heredar de esta clase para tener un control absoluto de auditoría.

```csharp
public abstract class AuditableEntity
{
    public Guid Id { get; protected set; }
    public DateTime CreatedAt { get; set; }
    public string? CreatedBy { get; set; }
    public DateTime? LastModifiedAt { get; set; }
    public string? LastModifiedBy { get; set; }
}
```
