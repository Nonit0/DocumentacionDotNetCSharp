---
title: "Role"
tags: [csharp, dotnet, dotnet5, proyecto-bazar, domain, entity, security, rbac]
draft: false
---
# Role

- **Resumen:** Define los diferentes niveles de acceso o perfiles de usuario (ADMIN, CLIENTE, OPERARIO).
- **Capa y Responsabilidad:** Capa `Domain`. Implementa la base del Control de Acceso basado en Roles (RBAC).
- **Código Fuente:**
```csharp 
public class Role : BaseEntity
{
	public string Name { get; set; } // "ADMIN", "CLIENTE", "OPERARIO"
	public virtual ICollection<User> Users { get; set; }
}
``` 

### Análisis Técnico
- **Colección Inversa:** Mantiene una `ICollection<User>` virtual para permitir búsquedas desde el rol hacia sus usuarios (relación 1:N).
