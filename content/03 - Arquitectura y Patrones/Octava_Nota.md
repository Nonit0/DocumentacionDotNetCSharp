---
title: "📝 Nota 08: Inyección de Dependencias"
tags:
  - csharp9
  - dotnet5/patrones
  - performance
  - dotnet5/di
  - avanzado
  - dotnet5
draft: false
---
# Inyección de Dependencias (DI) y Ciclos de Vida en.NET 5

### Qué es y para qué sirve

La Inyección de Dependencias (DI) es un patrón de diseño que implementa el principio de Inversión de Control (IoC). En.NET 5, el contenedor de DI nativo (configurado en `Startup.cs`) gestiona cuándo se crean, se comparten y se destruyen las instancias de nuestros servicios. Existen tres ciclos de vida estrictos que dictan este comportamiento:

1. **Transient (Transitorio):** Se crea una nueva instancia _cada vez_ que se solicita.
    
2. **Scoped (Con ámbito):** Se crea una única instancia _por cada petición HTTP_. Todos los componentes que pidan este servicio durante la misma solicitud web recibirán la misma instancia.
    
3. **Singleton:** Se crea una única instancia la primera vez que se solicita y se comparte en _toda la aplicación_ hasta que el servidor se reinicia.
    

### Cuándo usarlo en proyectos reales

- **Transient:** Para servicios muy ligeros y sin estado (stateless), como utilidades de formateo o generadores de UUIDs.
    
- **Scoped:** Es el estándar para aplicaciones web. Ideal para los Servicios de la capa de Aplicación (ej. `OrderService`) y los Repositorios, ya que permite compartir estado durante una transacción y luego limpiarlo. Por defecto, el `DbContext` de Entity Framework Core se registra como Scoped.
    
- **Singleton:** Para cachés en memoria, servicios de configuración estática o clientes HTTP estables (aunque para esto último es mejor `IHttpClientFactory`).
    

### Buenas prácticas

Diseña siempre tus servicios (capa de Aplicación) para que no mantengan estado global. Si necesitas compartir información entre peticiones, usa una base de datos o una caché distribuida (como Redis), no variables estáticas ni servicios Singleton mutables.

### Errores comunes de juniors y cómo evitarlos

**Error (Captive Dependency):** Inyectar un servicio `Scoped` (como un Repositorio o el `DbContext`) dentro de un servicio `Singleton` (como un Background Worker). **Consecuencia:** El servicio Singleton retendrá la referencia del DbContext para siempre. Esto provoca que el contexto de base de datos nunca se destruya, acumulando memoria y lanzando excepciones de concurrencia cuando múltiples hilos intenten usar esa misma conexión a MariaDB simultáneamente. **Solución:** Si un Singleton necesita usar un servicio Scoped, debe inyectar `IServiceScopeFactory`, crear un ámbito temporal (`CreateScope()`), usar el servicio y luego desechar el ámbito.

### Ejemplo de código: El Anti-patrón y la Solución en.NET 5
```csharp
using Microsoft.Extensions.DependencyInjection;
using System;
using System.Threading.Tasks;
using Bazar.Domain.Interfaces;

namespace Bazar.Infrastructure.BackgroundServices
{
    // Servicio Singleton que corre en background
    public class ReportGeneratorBackgroundService 
    {
        private readonly IServiceScopeFactory _scopeFactory;

        // BIEN: Inyectamos la fábrica de scopes, NO el repositorio directamente
        public ReportGeneratorBackgroundService(IServiceScopeFactory scopeFactory)
        {
            _scopeFactory = scopeFactory;
        }

        public async Task GenerateDailyReportAsync()
        {
            // Creamos un scope temporal explícito (como si fuera una petición HTTP)
            using (var scope = _scopeFactory.CreateScope())
            {
                // Resolvemos el servicio Scoped de forma segura
                var orderRepository = scope.ServiceProvider.GetRequiredService<IOrderRepository>();
                
                var orders = await orderRepository.GetTodayOrdersAsync();
                // Procesar reporte...
            } 
            // Al salir del 'using', el scope se destruye limpiamente junto con el DbContext.
        }
    }
}
```