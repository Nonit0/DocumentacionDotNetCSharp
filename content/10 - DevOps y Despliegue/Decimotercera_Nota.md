---
title: "📝 Nota 13: Dockerfile Multistage"
tags:
  - csharp9
  - dotnet5/despliegue
  - performance
  - devops
  - docker
  - avanzado
draft: false
---
# Dockerfile Multistage para.NET 5

### Qué es y para qué sirve

Dockerizar una aplicación escrita en lenguajes compilados (como Java o C#) presenta un desafío: para compilar el código necesitas el **SDK** (Software Development Kit), que pesa cientos de megabytes e incluye compiladores y herramientas. Sin embargo, para _ejecutar_ la aplicación en producción, solo necesitas el **Runtime** (entorno de ejecución), que es muchísimo más ligero. Un **Dockerfile Multistage** (Multi-etapa) resuelve este problema creando capas temporales. Utiliza una imagen pesada (el SDK de.NET 5) para restaurar paquetes, compilar y publicar el código. Luego, en una segunda etapa, arranca desde una imagen limpia y muy ligera (el Runtime de.NET 5) y simplemente _copia_ los binarios ya compilados de la primera etapa.

### Cuándo usarlo en proyectos reales

**Siempre**. Si despliegas tu API en Kubernetes, Azure Container Apps, AWS ECS o en un simple servidor VPS, debes usar builds multi-etapa. Garantiza que las imágenes que viajan por la red sean diminutas y que el tiempo de arranque de tu aplicación sea de milisegundos.

### Buenas prácticas

- **Caché de capas (Layer Caching):** En Docker, el orden de las instrucciones importa. Primero debes copiar _únicamente_ los archivos `.csproj` y ejecutar `dotnet restore`. De esta forma, si cambias una línea de código C#, Docker no volverá a descargar todos los paquetes NuGet de internet, reutilizando la caché.
    
- **Usuarios No-Root:** Por seguridad, las imágenes de producción nunca deben ejecutar la API como usuario administrador (`root`)..NET permite configurar el contenedor para correr bajo un perfil de usuario restringido.
    

### Errores comunes de juniors y cómo evitarlos

**Error:** Usar la imagen `mcr.microsoft.com/dotnet/sdk:5.0` como imagen final de producción. **Consecuencia:** Tu contenedor final pesará cerca de 800 MB (en lugar de 150 MB). Además, estás incluyendo compiladores en tu entorno de producción, lo que significa que si un atacante vulnera tu contenedor, podría compilar código malicioso directamente desde tu servidor (enorme riesgo de seguridad). **Solución:** Usar `mcr.microsoft.com/dotnet/aspnet:5.0` en la etapa final del Dockerfile.

### Ejemplo de código: Dockerfile Limpio y Optimizado
```dockerfile
# ==========================================
# ETAPA 1: BUILD (SDK Pesado para compilar)
# ==========================================
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build-env
WORKDIR /app

# Copiamos solo los archivos de proyecto para aprovechar la caché de Docker
COPY src/Bazar.Api/*.csproj./src/Bazar.Api/
COPY src/Bazar.Application/*.csproj./src/Bazar.Application/
COPY src/Bazar.Domain/*.csproj./src/Bazar.Domain/
COPY src/Bazar.Infrastructure/*.csproj./src/Bazar.Infrastructure/

# Restauramos los paquetes NuGet (Esto se cacheará si no cambian los csproj)
RUN dotnet restore src/Bazar.Api/Bazar.Api.csproj

# Ahora sí, copiamos el resto del código fuente
COPY src/./src/

# Compilamos y publicamos en modo Release (optimizado)
RUN dotnet publish src/Bazar.Api/Bazar.Api.csproj -c Release -o /app/out

# ==========================================
# ETAPA 2: RUNTIME (Imagen ligera para Producción)
# ==========================================
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app

# Exponemos el puerto estándar 80
EXPOSE 80

# Copiamos ÚNICAMENTE los binarios compilados desde la etapa 'build-env'
COPY --from=build-env /app/out.

# (Opcional pero recomendado) Cambiamos a un usuario sin privilegios root por seguridad
# USER 1000

# Punto de entrada de la aplicación
ENTRYPOINT
```