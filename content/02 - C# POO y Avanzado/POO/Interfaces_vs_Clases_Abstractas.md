---
title: "Interfaces vs Clases Abstractas"
tags: [csharp, dotnet]
draft: false
---
# Interfaces vs Clases Abstractas en C#

Ambas son fundamentales en la Programación Orientada a Objetos, pero tienen propósitos distintos:

## Interfaz (`interface`)
Define un **contrato**. Qué debe hacer una clase, pero no cómo (aunque desde C# 8 permiten implementaciones por defecto).
- Una clase puede implementar **múltiples** interfaces.
- Ideal para definir capacidades (ej: `ILoggable`, `IDisposable`).

```csharp
public interface IVehiculo 
{
    void Arrancar();
}
```

## Clase Abstracta (`abstract class`)
Define la **identidad** base de un objeto. Puede tener código implementado y estado (campos).
- Una clase solo puede heredar de **una** clase abstracta.
- Ideal para compartir lógica base entre objetos fuertemente relacionados.

```csharp
public abstract class VehiculoBase
{
    public int Ruedas { get; set; }
    
    // Método que las clases hijas están obligadas a implementar
    public abstract void Mover(); 
}
```
