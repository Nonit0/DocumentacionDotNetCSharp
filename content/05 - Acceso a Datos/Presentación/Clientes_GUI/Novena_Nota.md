---
title: "📝 Nota 09: Angular Interceptors RxJS"
tags:
  - angular16
  - frontend
  - jwt
  - seguridad
draft: false
---
# Integración Angular: Interceptores HTTP y RxJS con JWT

### Qué es y para qué sirve

En una arquitectura Full Stack con Angular y.NET 5, el frontend actúa como un cliente completamente autónomo y sin estado (Stateless). Para consumir los endpoints protegidos, Angular debe incluir el token {{JWT}} en la cabecera `Authorization: Bearer <token>` de cada petición HTTP. En lugar de añadir este token manualmente en cada llamada de nuestros servicios (`HttpClient`), Angular nos proporciona los **HttpInterceptors**. Un interceptor es un middleware del lado del cliente que captura las peticiones salientes, las clona, les inyecta el token y las envía. También captura las respuestas entrantes mediante **RxJS** para gestionar errores globales (como un 401 Unauthorized).

### Cuándo usarlo en proyectos reales

Los interceptores son obligatorios en cualquier SPA (Single Page Application) orientada al mundo empresarial. Se utilizan para inyectar tokens, añadir cabeceras de idioma (`Accept-Language`), mostrar globalmente barras de carga (spinners) en la UI y centralizar el manejo de errores HTTP.

### Buenas prácticas

El token JWT nunca debe guardarse en `localStorage` si manejas datos extremadamente sensibles (debido a ataques XSS), aunque es la práctica más común. A nivel Senior, se recomienda guardarlo en memoria (estado de la app con NgRx) o usar cookies `HttpOnly` gestionadas por el backend. Además, el interceptor debe utilizar el operador `catchError` de RxJS para interceptar códigos 401 (token expirado) e iniciar el flujo silencioso de "Refresh Token" o redirigir al usuario al Login.

### Errores comunes de juniors y cómo evitarlos

**Error:** Modificar el objeto `HttpRequest` original directamente dentro del interceptor. **Consecuencia:** Angular impone que las peticiones HTTP sean inmutables para evitar comportamientos impredecibles en reintentos de red. Si lo modificas directamente, Angular lanzará un error en tiempo de ejecución. **Solución:** Se debe usar el método `req.clone()` para crear una nueva instancia de la petición con las cabeceras modificadas.

### Ejemplo de código: Interceptor JWT Limpio en Angular
```tsx
import { Injectable } from '@angular/core';
import { HttpRequest, HttpHandler, HttpEvent, HttpInterceptor, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { AuthService } from '../services/auth.service';
import { Router } from '@angular/router';

@Injectable()
export class JwtInterceptor implements HttpInterceptor {

  constructor(private authService: AuthService, private router: Router) {}

  intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    
    // 1. Obtenemos el token del servicio de autenticación
    const token = this.authService.getToken();

    // 2. Si existe, CLONAMOS la petición (regla de inmutabilidad) y añadimos el header
    if (token) {
      request = request.clone({
        setHeaders: {
          Authorization: `Bearer ${token}`
        }
      });
    }

    // 3. Enviamos la petición y usamos RxJS para reaccionar a la respuesta
    return next.handle(request).pipe(
      catchError((error: HttpErrorResponse) => {
        // Interceptamos respuestas del backend.NET 5
        if (error.status === 401) {
          // El token ha expirado o es inválido. Redirigir a login o ejecutar Refresh Token.
          this.authService.logout();
          this.router.navigate(['/login']);
        }
        
        // Propagamos el error para que el componente también pueda reaccionar si lo desea
        return throwError(() => error);
      })
    );
  }
}
```