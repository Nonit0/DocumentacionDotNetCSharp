---
title: Startup.cs
tags:
  - csharp
  - dotnet
  - dotnet5
  - api
  - dependency-injection
  - middleware
  - composition-root
draft: false
---

- **Resumen:** Centraliza el registro de inyección de dependencias (IoC Container) y define el orden del pipeline de Middlewares que procesan cada petición HTTP.

- **Capa y Responsabilidad:** Pertenece a la capa **API**. Actúa como el **Composition Root** de la Clean Architecture. Conoce todas las capas (Infrastructure, Application, Domain) única y exclusivamente para instanciarlas y enlazarlas.

- **Código Fuente:**

```csharp
// ... imports omitidos para brevedad ...

namespace Bazar.API
{
    public class Startup
    {
        public IConfiguration Configuration { get; }

        public Startup(IConfiguration configuration) 
        { 
            Configuration = configuration; 
        }

        public void ConfigureServices(IServiceCollection services)
        {
            // 1. Configuración de CORS para el frontend en Angular
            services.AddCors(options => 
            {
                options.AddPolicy("PoliticaBazar", builder => builder
                    .WithOrigins("http://localhost:4200") 
                    .AllowAnyMethod()
                    .AllowAnyHeader());
            });

            // 2. Configuración de Base de Datos (MySQL)
            services.AddDbContext(options => 
            {
                var connectionString = Configuration.GetConnectionString("DefaultConnection");
                options.UseMySql(connectionString, ServerVersion.AutoDetect(connectionString),
                    b => b.MigrationsAssembly("Bazar.Infrastructure"));
            });

            services.AddControllers();

            // 3. Documentación de la API
            services.AddSwaggerGen(c => 
            { 
                /* ... Config Swagger ... */ 
            });

            // 4. Autenticación JWT
            services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddJwtBearer(options => 
                { 
                    /* ... Config JWT ... */ 
                });

            // 5. Inyección de Dependencias (Contratos -> Implementaciones)
            services.AddScoped<IProductRepository, ProductRepository>();
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            // Middleware global para atrapar errores antes de que rompan la app
            app.UseMiddleware(); // Excepciones Globales

            if (env.IsDevelopment()) 
            {
                app.UseDeveloperExceptionPage();
                app.UseSwagger();
                app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "Bazar.API v1"));
            }

            // Orden crítico de los Middlewares en .NET
            app.UseRouting();
            app.UseCors("PoliticaBazar");
            
            app.UseAuthentication(); // 1º ¿Quién eres?
            app.UseAuthorization();  // 2º ¿Qué puedes hacer?

            app.UseEndpoints(endpoints => 
            { 
                endpoints.MapControllers(); 
            });
        }
    }
}
```

- **Análisis Técnico:**
    - ~={cyan} **C# =~ConfigureServices**: Registra los servicios. Vemos el mapeo de `IProductRepository` a `ProductRepository` con ciclo de vida `Scoped` (instancia por request HTTP). También enlaza EF Core especificando explícitamente que las migraciones residen en `Bazar.Infrastructure` (una decisión arquitectónica clave).
    - ~={cyan} **C# =~Configure**: Define el canal de paso de cada HTTP Request. El orden es crítico aquí. Introduce un Middleware personalizado (`ExceptionMiddleware`) en la primera capa para atrapar cualquier error no manejado, sigue con el enrutamiento, CORS, identidad (Autenticación/Autorización) y termina en los Controladores.
- **Buenas Prácticas Aplicadas:**
    - **Composition Root:** ~={cyan} **C# =~[[Startup.cs]]** es el único lugar donde la capa de presentación se acopla a la infraestructura concreta. Las demás capas permanecen desacopladas (inversión de dependencias).
    - **Seguridad y CORS Restrictivo:** Declara una política CORS explícita apuntando únicamente a `http://localhost:4200` en lugar de permitir todos los orígenes (`AllowAnyOrigin`), protegiendo la API en desarrollo/producción.
    - **Single Source of Truth de Configuración:** Usa la interfaz `IConfiguration` para absorber dinámicamente secrets o strings de conexión en vez de hardcodearlos en el archivo.