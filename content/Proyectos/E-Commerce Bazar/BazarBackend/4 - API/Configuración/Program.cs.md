---
title: "Program.cs"
tags: [csharp, dotnet, dotnet5, api, configuration, host]
draft: false
---

- **Resumen:** Es el punto de entrada principal (Entry Point) de la aplicación web. Construye y ejecuta el host que aloja el servidor Kestrel.
- **Capa y Responsabilidad:** Pertenece a la capa **API**. Su única responsabilidad es el arranque a muy bajo nivel del proceso y delegar la configuración detallada a la clase ~={cyan} **C# =~[[Startup.cs]]**.
- **Código Fuente:**
```csharp
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Hosting;
  using Microsoft.Extensions.Logging;

  namespace Bazar.API
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              CreateHostBuilder(args).Build().Run();
          }

          public static IHostBuilder CreateHostBuilder(string[] args) =>
              Host.CreateDefaultBuilder(args)
                  .ConfigureWebHostDefaults(webBuilder =>
                  {
                      webBuilder.UseStartup<Startup>();
                  });
      }
  }
```
- **Análisis Técnico:**
    - **`Host.CreateDefaultBuilder`**: Configura valores por defecto esenciales en .NET 5, como la carga de ~={yellow} **{ } =~[[appsettings.json]]** , variables de entorno, configuración de logging estándar (Consola, Debug) y el uso de Kestrel como servidor web.
    - **`UseStartup<Startup>()`**: Enlaza este host genérico con la clase ~={cyan} **C# =~[[Startup.cs]]**, donde residen las inyecciones de dependencias y el middleware.
- **Buenas Prácticas Aplicadas:**
    - **Aislamiento del Host:** Mantiene el método ~={cyan} **C# =~Main** extremadamente limpio. La construcción del host se separa en ~={cyan} **C# =~CreateHostBuilder()**, lo cual es vital para herramientas de Entity Framework Core o tests de integración, que necesitan construir el host sin llegar a correr el servidor (`.Run()`).


