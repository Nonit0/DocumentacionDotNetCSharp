---
title: "ProductosController"
tags: [csharp, dotnet, dotnet5, api, products, repository-pattern]
draft: false
---
- **Resumen:** Controlador para la gestión de productos, permitiendo listar, filtrar por ID y realizar operaciones CRUD bajo autorización.
- **Capa y Responsabilidad:** Capa **API**. Orquestador de las peticiones relacionadas con el catálogo de productos, delegando la persistencia al patrón Repositorio.
- **Código Fuente:**
```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController : ControllerBase
{
    private readonly IProductRepository _productRepository;

    [HttpGet]
    public async Task<ActionResult<IEnumerable<ProductDto>>> Get() { ... }

    [HttpPost]
    [Authorize(Roles = "ADMIN,OPERARIO")]
    public async Task<ActionResult<ProductDto>> Post(ProductDto dto) { ... }
}
```
- **Análisis Técnico:**
    - **Inyección de Dependencias:** Recibe `IProductRepository` por constructor, cumpliendo con el principio de Inversión de Dependencias.
    - **Mapeo Manual a DTO:** Transforma las entidades de dominio en objetos de transferencia de datos (DTO) para desacoplar la base de datos de la respuesta JSON.
    - **Autorización por Roles:** Restringe las operaciones de escritura solo a usuarios con roles específicos.
- **Buenas Prácticas Aplicadas:**
    - **Desacoplamiento:** El controlador no conoce la implementación concreta de la base de datos (EF Core), solo la interfaz.
    - **Asincronía:** Todos los métodos son `async`, evitando bloquear hilos del servidor durante operaciones de E/S.