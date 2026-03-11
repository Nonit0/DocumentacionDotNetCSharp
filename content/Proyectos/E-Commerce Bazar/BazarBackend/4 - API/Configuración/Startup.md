---

title: "Startup.cs"

tags: [csharp, dotnet, dotnet5, api, dependency-injection, middleware, composition-root]

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

public Startup(IConfiguration configuration) { Configuration = configuration; }

public void ConfigureServices(IServiceCollection services)

{

services.AddCors(options => {

options.AddPolicy("PoliticaBazar", builder => builder

.WithOrigins("http://localhost:4200") // Angular

.AllowAnyMethod().AllowAnyHeader());

});

services.AddDbContext<ApplicationDbContext>(options => {

var connectionString = Configuration.GetConnectionString("DefaultConnection");

options.UseMySql(connectionString, ServerVersion.AutoDetect(connectionString),

b => b.MigrationsAssembly("Bazar.Infrastructure"));

});

services.AddControllers();

services.AddSwaggerGen(c => { /* ... Config Swagger ... */ });

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)

.AddJwtBearer(options => { /* ... Config JWT ... */ });

services.AddScoped<IProductRepository, ProductRepository>();

}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)

{

app.UseMiddleware<ExceptionMiddleware>(); // Excepciones Globales

if (env.IsDevelopment()) {

app.UseDeveloperExceptionPage();

app.UseSwagger();

app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "Bazar.API v1"));

}

app.UseRouting();

app.UseCors("PoliticaBazar");

app.UseAuthentication();

app.UseAuthorization();

app.UseEndpoints(endpoints => { endpoints.MapControllers(); });

}

}

}
```
