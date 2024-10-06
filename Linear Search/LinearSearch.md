A continuación te muestro cómo implementar **Linear Search** en C# y JavaScript. Linear Search es un algoritmo sencillo donde se recorre un array de principio a fin, comparando cada elemento con el objetivo hasta encontrarlo.

### C# - Implementación de Linear Search

```csharp
using System;

public class LinearSearchExample
{
    // Método para realizar una búsqueda lineal
    public static int LinearSearch(int[] arr, int target)
    {
        // Recorremos el array
        for (int i = 0; i < arr.Length; i++)
        {
            // Comparación de cada elemento con el objetivo
            if (arr[i] == target)
            {
                return i; // Si encontramos el objetivo, retornamos su índice
            }
        }
        return -1; // Si no encontramos el objetivo, retornamos -1
    }

    public static void Main(string[] args)
    {
        int[] arr = { 10, 23, 45, 70, 11, 15 };
        int target = 70;

        int result = LinearSearch(arr, target);

        if (result != -1)
        {
            Console.WriteLine($"Elemento {target} encontrado en el índice {result}");
        }
        else
        {
            Console.WriteLine($"Elemento {target} no encontrado en el array");
        }
    }
}
```

### Explicación del código en C#:
1. **Recorrido**: Utilizamos un bucle `for` para recorrer todo el array.
2. **Comparación**: En cada iteración, comparamos el valor actual con el objetivo (`target`).
3. **Resultado**: Si el elemento coincide con el objetivo, retornamos el índice donde se encuentra. Si llegamos al final del array sin encontrarlo, devolvemos `-1` para indicar que el elemento no está presente.

---

### JavaScript - Implementación de Linear Search

```javascript
// Función para realizar una búsqueda lineal
function linearSearch(arr, target) {
    // Recorremos el array
    for (let i = 0; i < arr.length; i++) {
        // Comparación de cada elemento con el objetivo
        if (arr[i] === target) {
            return i; // Si encontramos el objetivo, retornamos su índice
        }
    }
    return -1; // Si no encontramos el objetivo, retornamos -1
}

// Ejemplo de uso:
let arr = [10, 23, 45, 70, 11, 15];
let target = 70;

let result = linearSearch(arr, target);

if (result !== -1) {
    console.log(`Elemento ${target} encontrado en el índice ${result}`);
} else {
    console.log(`Elemento ${target} no encontrado en el array`);
}
```

### Explicación del código en JavaScript:
1. **Recorrido**: Usamos un bucle `for` para recorrer todos los elementos del array.
2. **Comparación**: Comparamos cada elemento del array con el valor objetivo (`target`).
3. **Resultado**: Si encontramos el elemento, retornamos su índice. Si no se encuentra, devolvemos `-1`.

---

### Flujo del Algoritmo en ambas implementaciones:
1. **Recorrido completo**: Se inicia en el primer elemento del array y se avanza elemento por elemento hasta el final.
2. **Comparación de cada elemento**: Se compara el valor de cada elemento con el valor que estamos buscando (objetivo).
3. **Éxito o fracaso**: Si el valor se encuentra, el índice se devuelve inmediatamente. Si se recorre todo el array sin encontrar el valor, se devuelve `-1` para indicar que no existe en el array.

### Complejidad de Linear Search:
- **Peor caso**: O(n) - Si el objetivo está al final del array o no está presente.
- **Mejor caso**: O(1) - Si el objetivo es el primer elemento del array.
- **Promedio**: O(n) - En promedio, el elemento se encontrará en el centro del array.

Linear Search es simple pero puede ser ineficiente para arrays grandes, ya que recorre todo el array en el peor de los casos. Sin embargo, es útil cuando los datos no están ordenados o no se requiere una solución optimizada.