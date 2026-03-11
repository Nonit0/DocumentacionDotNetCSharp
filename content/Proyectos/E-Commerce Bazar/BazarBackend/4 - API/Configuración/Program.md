---
title: "Program.cs"
tags: [csharp, dotnet, dotnet5, api, configuration, host]
draft: false
---

- **Resumen:** Es el punto de entrada principal (Entry Point) de la aplicación web. Construye y ejecuta el host que aloja el servidor Kestrel.
- **Capa y Responsabilidad:** Pertenece a la capa **API**. Su única responsabilidad es el arranque a muy bajo nivel del proceso y delegar la configuración detallada a la clase `Startup`.
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
