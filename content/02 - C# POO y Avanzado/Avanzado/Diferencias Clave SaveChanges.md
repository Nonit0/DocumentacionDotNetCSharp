# 📝 Entity Framework Core: SaveChanges vs SavedChanges
Esta nota explica la diferencia fundamental entre el **método** de ejecución y el **evento** de notificación en EF Core.

---
## 1. SaveChanges() → El Método (La Acción) 🛠️
Es la función que **tú llamas** manualmente para persistir los datos. Sin esto, nada de lo que hagas en el `_context` llegará a la base de datos SQL.

- **Tipo:** Método (Verbo).
- **Propósito:** Traducir los cambios en memoria a comandos SQL (`INSERT`, `UPDATE`, `DELETE`).
- **Retorno:** Un entero (`int`) con el número de filas afectadas.

### Ejemplo en Controlador:
```csharp
[HttpPost]
public ActionResult Create(CamionDTO dto)
{
    var nuevoCamion = new Camion { Matricula = dto.Matricula };
    _context.Camion.Add(nuevoCamion);

    // GUARDAR CAMBIOS: Acción manual
    _context.SaveChanges(); 

    return Ok();
}
```

---
## 2. SavedChanges → El Evento (La Reacción) 🔔
Es una notificación automática que **lanza .NET** después de que `SaveChanges` termina con éxito. No guarda nada, solo "avisa".

- **Tipo:** Evento (Sustantivo/Estado).
- **Propósito:** Ejecutar lógica secundaria (Logs, auditoría, limpiar caché) una vez los datos están a salvo.
- **Configuración:** Se suele definir en el constructor del `DbContext`.

### Ejemplo de suscripción:
```csharp
public class TransportesContext : DbContext
{
    public TransportesContext()
    {
        // "Cuando se guarde con éxito, avísame"
        this.SavedChanges += (sender, args) => 
        {
            Console.WriteLine($"Se guardaron {args.EntitiesSavedCount} entidades.");
        };
    }
}
```

---
## 📊 Comparativa Rápida

| Característica | SaveChanges() | SavedChanges |
| :--- | :--- | :--- |
| **Naturaleza** | Método de ejecución. | Evento de notificación. |
| **Iniciador** | El programador (Tú). | El sistema (Entity Framework). |
| **Momento** | Cuando decides guardar. | Justo **después** de guardar. |
| **Uso Principal** | Persistir datos en la DB. | Auditoría y efectos secundarios. |

---
## 💡 Tip para el Debugger
Si quieres comprobar si una inserción ha funcionado correctamente mientras depuras en VS Code:

1. Pon el breakpoint **después** de la línea `_context.SaveChanges();`.
2. Revisa el valor de la variable entera que recibe el resultado:
   - Si es `> 0`: El guardado fue exitoso.
   - Si es `0`: No hubo cambios que guardar o algo falló silenciosamente.

---
#dotnet #entityframework #csharp #backend