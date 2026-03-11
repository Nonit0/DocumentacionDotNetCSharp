---
title: "IProductRepository"
tags: [csharp, dotnet, domain, interface, repository-pattern]
draft: false
---
- **Resumen:** Interfaz que define el contrato de persistencia para los productos, desacoplando el dominio de la infraestructura.
- **Capa y Responsabilidad:** Capa **Domain**. Define **qué** operaciones se pueden hacer con los productos, pero no **cómo** (la implementación va en Infrastructure).
- **Código Fuente:**
```csharp
public interface IProductRepository
{
    Task<IEnumerable<Product>> GetAllWithImagesAsync();
	Task<Product> GetByIdAsync(Guid id);
	Task<Product> AddAsync(Product product);
	Task<Product> UpdateAsync(Product product);
	Task DeleteAsync(Guid id);
}
```
- **Análisis Técnico:**
    - **Programación Asíncrona:** Todos los métodos devuelven `Task`, siguiendo el patrón `async/await` para escalabilidad.
- **Buenas Prácticas Aplicadas:**
    - **Inversión de Dependencias (D de SOLID):** Permite que la aplicación dependa de abstracciones en lugar de implementaciones concretas.
