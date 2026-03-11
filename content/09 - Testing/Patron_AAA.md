---
title: "El Patrón AAA en Testing"
tags: [csharp, dotnet]
draft: false
---
# Arrange, Act, Assert (AAA)

Es el estándar de la industria para estructurar pruebas unitarias (xUnit/NUnit).

1. **Arrange (Preparar):** Inicializar objetos, mocks (Moq) y variables.
2. **Act (Actuar):** Llamar al método que queremos probar.
3. **Assert (Afirmar):** Comprobar que el resultado es el esperado (FluentAssertions).
