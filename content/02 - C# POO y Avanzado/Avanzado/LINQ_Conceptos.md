---
title: "LINQ (Language Integrated Query)"
tags: [csharp, dotnet, linq]
draft: false
---
# LINQ (Language Integrated Query)

- **Resumen:** LINQ es un conjunto de extensiones integradas en el lenguaje C#, que nos permite trabajar de manera cómoda y rápida con colecciones de datos, como si de una base de datos se tratase. Es decir, podemos llevar a cabo inserciones, selecciones y borrados, así como operaciones sobre sus elementos.
- **Capa y Responsabilidad:** Fundamental para transformar Entidades en DTOs y realizar filtrados eficientes en memoria o contra base de datos.

### Análisis Técnico
- **Proyecciones:** Utiliza `.Select()` para transformar tipos complejos en objetos más sencillos, optimizando lo que se devuelve al cliente.
- **Deferred Execution:** Las consultas no se ejecutan hasta que se recorren (ej. un `foreach`) o se llaman a métodos materializadores como `.ToList()` o `.ToArray()`, lo que mejora el rendimiento.
- **Integración con EF Core:** Traduce las expresiones lambda directamente a sentencias SQL (ej. `WHERE`, `ORDER BY`).

### Buenas Prácticas Aplicadas
- **Proyecciones Limpias:** Evita traer todos los datos de la tabla. Usar `.Select()` asegura que solo viajan por la red las propiedades estrictamente necesarias para los DTOs.
- **Legibilidad:** Transforma bucles complejos y condicionales anidados en cadenas de métodos declarativos muy fáciles de leer y mantener.

### **Ejercicio de demostración**
- Suma de valores:
	Suponiendo que tenemos una lista de enteros, si queremos sumarlos podríamos hacer algo así:
```csharp
var valores = new List<int> {1,2,3,4,5,6,7,8,9};
var suma = 0;
foreach (var valor in valores)
{
    suma += valor;
}
```

- Funciones específicas
	O si por ejemplo, queremos buscar los números que sean pares:
```csharp
var valores = new List<int> {1,2,3,4,5,6,7,8,9};
var pares = new List<int>();
foreach (var valor in valores)
{
    if (valor % 2 == 0)
    {
        pares.Add(valor);
    }
}
```

- **Con LINQ:**
```csharp
var valores = new List<int> {1,2,3,4,5,6,7,8,9};
var suma = valores.Sum();
var pares = valores.Where(x => x % 2 == 0).ToList();
```

> [!info]- Ventajas de usar LINQ y Expresiones Lambda
> Como se puede comprobar, la lectura es mucho más clara, por lo que ganamos en mantenibilidad del código.
> 
> Todas estas operaciones las vamos a conseguir muy fácilmente gracias a los métodos de extensión para colecciones que nos ofrece el espacio de nombres "[System.Linq](https://docs.microsoft.com/es-es/dotnet/api/system.LINQ?view=netframework-4.8)" y a las "**expresiones [lambda](https://docs.microsoft.com/es-es/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)**".

Seguramente te hayas fijado en el `ToList()` del segundo caso. Esto es porque LINQ siempre nos va a devolver un objeto de tipo `IEnumerable<T>`, el cual debemos iterar. **Hasta que no lo iteremos, la consulta no se ha ejecutado todavía, y solo tenemos una expresión sobre una colección**, por eso invocamos `ToList()` para forzar la ejecución de la consulta.

>[!info]- Mas sobre consultas
Sobre la ejecución diferida de consultas se puede hablar largo y tendido ya que es una materia en sí misma. Para más información puedes consultar este [enlace](https://docs.microsoft.com/es-es/dotnet/framework/data/adonet/ef/language-reference/query-execution).

### **Ejemplos Reales**
- Supongamos una clase:
```csharp
public class Alumno
{
    public string Nombre { get; set; }

    public int Nota { get; set; }
}
```

- Y asumimos una colección como:
```csharp
var alumnos = new List<Alumno>
{
    new Alumno {Nombre = "Pedro", Nota = 5},
    new Alumno {Nombre = "Jorge", Nota = 8},
    new Alumno {Nombre = "Andres", Nota = 3}
};
```
#### **Operaciones posibles con LINQ:**

1. **Select**
   > [!example]- Expandir detalle y código
   > Nos va a permitir hacer una selección sobre la colección de datos, ya sea seleccionándolos todos, solo una parte o transformándolos:
   > ```csharp
   > var nombresAlumnos = alumnos.Select(x => x.Nombre).ToList();
   > ```

2. **Where**
   > [!example]- Expandir detalle y código
   > Nos permite seleccionar una colección a partir de otra con los objetos que cumplan las condiciones especificadas:
   > ```csharp
   > var alumnosAprobados = alumnos.Where(x => x.Nota >= 5).ToList();
   > ```

3. **First / Last**
   > [!example]- Expandir detalle y código
   > Esta extensión nos va a permitir obtener respectivamente el primer y el último objeto de la colección. Esto es especialmente útil si la colección está ordenada.
   > ```csharp
   > var primero = alumnos.First();
   > var ultimo = alumnos.Last();
   > ```

4. **OrderBy / OrderByDescending**
   > [!example]- Expandir detalle y código
   > Gracias a este método, vamos a poder ordenar la colección en base a un criterio de ordenación que le indicamos mediante una expresión lambda. Análogamente, también existe `OrderByDescending`, el cual va a ordenar la colección de manera inversa según el criterio:
   > ```csharp
   > var ordenadoMenorAMayor = alumnos.OrderBy(x => x.Nota).ToList();
   > var ordenadoMayorAMenos = alumnos.OrderByDescending(x => x.Nota).ToList();
   > ```

5. **Sum**
   > [!example]- Expandir detalle y código
   > Como hemos visto más arriba, nos va a permitir sumar la colección:
   > ```csharp
   > var sumaNotas = alumnos.Sum(x => x.Nota);
   > ```

6. **Max / Min**
   > [!example]- Expandir detalle y código
   > Gracias a esta extensión, vamos a poder obtener los valores máximo y mínimo de la colección:
   > ```csharp
   > var notaMaxima = alumnos.Max(x => x.Nota);
   > var notaMinima = alumnos.Min(x => x.Nota);
   > ```

7. **Average**
   > [!example]- Expandir detalle y código
   > Este método nos va a devolver la media aritmética de los valores (numéricos) de los elementos que le indiquemos de la colección:
   > ```csharp
   > var media = alumnos.Average(x => x.Nota);
   > ```

8. **All / Any**
   > [!example]- Expandir detalle y código
   > Con este último operador, vamos a poder comprobar si todos o alguno de los valores de la colección cumplen el criterio que le indiquemos:
   > ```csharp
   > var todosAprobados = alumnos.All(x => x.Nota >= 5);
   > var algunAprobado = alumnos.Any(x => x.Nota >= 5);
   > ```

9. **Sintaxis integrada (Query Syntax)**
   > [!example]- Expandir detalle y código
> Aunque en los ejemplos anteriores hemos visto el uso directo de los métodos de extensión (Fluent Syntax), otra de las grandes ventajas que tiene LINQ es que permite crear expresiones directamente en el código de manera similar a si escribiésemos SQL directamente en C#.
> 
> ```csharp
> var resultado = from alumno in alumnos
>                 where alumno.Nota >= 5
>                 orderby alumno.Nota
>                 select alumno;
> ```
> 
> Esto nos devolverá la lista de alumnos que tienen una nota superior o igual a 5, ordenados por nota ascendentemente. ¿No es algo casi mágico?

---
## Ventajas y Desventajas
Ahora que hemos visto un poco por dónde pisamos, es hora de hablar sobre lo que nos aporta utilizar LINQ frente a la iteración tradicional de colecciones.

* **Desventaja (Rendimiento):** La principal (y casi única) desventaja es que LINQ añade una ligera sobrecarga, haciéndolo un poco más lento que usar bucles `for` o `foreach` puros para iterar la colección. Por supuesto, esto no es apreciable en prácticamente ninguna situación convencional, pero en entornos críticos donde cada milisegundo cuenta, debes conocer que tiene un impacto. 
* **Ventaja (Legibilidad):** El código es mucho más legible y limpio, ya que utiliza una sintaxis muy declarativa (dices *qué* quieres obtener, no *cómo* lo vas a buscar paso a paso). 
* **Ventaja (Acceso Unificado a Datos):** Nos ofrece una manera estandarizada de consultar datos, sin importar su origen o tipo. Podemos utilizar LINQ para trabajar con bases de datos (LINQ to Entities), con XML (LINQ to XML), con objetos en memoria (LINQ to Objects), ¡y hasta con APIs de terceros como [Twitter](https://github.com/JoeMayo/LINQToTwitter)!
## Resumiendo
- Pese a que en esta nota solo hemos hecho una pequeña introducción con las extensiones más frecuentes de LINQ (créeme que es una lista muy pequeña... te recomiendo explorar el espacio de nombres `System.Linq` para ver todas sus opciones), es una **herramienta increíblemente potente**. Tanto, que otros lenguajes de programación han implementado características similares. 
- Si bien es cierto que existe una merma de rendimiento respecto a iterar el bucle directamente, el rendimiento "perdido" en el 99,99% de los casos se compensa con creces con el beneficio que aporta tener un código claro, legible y altamente mantenible.