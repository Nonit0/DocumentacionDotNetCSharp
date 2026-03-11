---
title: "Configuración Base del DbContext"
tags: [csharp, dotnet]
draft: false
---
# DbContext: Buenas Prácticas

Sobrescribir el método `SaveChangesAsync` nos permite automatizar la auditoría de nuestras entidades antes de que toquen la base de datos.

```csharp
public override Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
{
    foreach (var entry in ChangeTracker.Entries<AuditableEntity>())
    {
        if (entry.State == EntityState.Added)
            entry.Entity.CreatedAt = DateTime.UtcNow;
            
        if (entry.State == EntityState.Modified)
            entry.Entity.LastModifiedAt = DateTime.UtcNow;
    }
    return base.SaveChangesAsync(cancellationToken);
}
```
