Aquí te muestro cómo implementar **Bubble Sort** en C# y JavaScript, junto con una explicación de los intercambios y cómo se puede optimizar el rendimiento básico del algoritmo.

### C# - Implementación de Bubble Sort

```csharp
using System;

public class BubbleSortExample
{
    public static void BubbleSort(int[] arr)
    {
        int n = arr.Length;
        bool swapped;

        // Recorremos todos los elementos del array.
        for (int i = 0; i < n - 1; i++)
        {
            swapped = false;
            // Últimos i elementos ya están en su lugar correcto.
            for (int j = 0; j < n - 1 - i; j++)
            {
                // Intercambiamos si el elemento actual es mayor que el siguiente.
                if (arr[j] > arr[j + 1])
                {
                    // Realizamos el intercambio.
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;

                    swapped = true; // Indicamos que hubo un intercambio.
                }
            }

            // Si no se intercambió ningún elemento, el array ya está ordenado.
            if (!swapped)
                break;
        }
    }

    public static void Main(string[] args)
    {
        int[] arr = { 64, 34, 25, 12, 22, 11, 90 };
        Console.WriteLine("Array original:");
        Console.WriteLine(string.Join(", ", arr));

        BubbleSort(arr);

        Console.WriteLine("Array ordenado:");
        Console.WriteLine(string.Join(", ", arr));
    }
}
```

### Explicación de la Implementación en C#:
1. **Intercambio**: Cuando se detecta que un elemento es mayor que el siguiente, intercambiamos sus posiciones utilizando una variable temporal (`temp`).
2. **Optimización**: El bucle `swapped` indica si hubo intercambios durante la pasada. Si no hay intercambios, significa que el array ya está ordenado y podemos salir del bucle para evitar iteraciones innecesarias.
3. **Reducción del rango**: En cada iteración del bucle externo, el rango de comparación se reduce porque los elementos más grandes se "burbujearán" hacia el final del array, lo que permite optimizar aún más el proceso.

---

### JavaScript - Implementación de Bubble Sort

```javascript
function bubbleSort(arr) {
    let n = arr.length;
    let swapped;

    // Recorremos todos los elementos del array.
    for (let i = 0; i < n - 1; i++) {
        swapped = false;
        // Últimos i elementos ya están en su lugar correcto.
        for (let j = 0; j < n - 1 - i; j++) {
            // Intercambiamos si el elemento actual es mayor que el siguiente.
            if (arr[j] > arr[j + 1]) {
                // Realizamos el intercambio.
                let temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;

                swapped = true; // Indicamos que hubo un intercambio.
            }
        }

        // Si no se intercambió ningún elemento, el array ya está ordenado.
        if (!swapped) break;
    }
}

// Ejemplo de uso:
let arr = [64, 34, 25, 12, 22, 11, 90];
console.log("Array original:", arr);

bubbleSort(arr);

console.log("Array ordenado:", arr);
```

### Explicación de la Implementación en JavaScript:
1. **Intercambio**: Si el elemento actual es mayor que el siguiente, se intercambian utilizando una variable temporal (`temp`).
2. **Optimización**: Al igual que en C#, si no se realizan intercambios en una pasada, significa que el array ya está ordenado y se detiene el bucle con la instrucción `break`.
3. **Reducción del rango**: En cada iteración, el tamaño del rango de comparación disminuye (`n - 1 - i`), porque los elementos más grandes ya están en su posición correcta al final del array.

---

### Optimización en Bubble Sort:

- **Uso del indicador `swapped`**: Una de las optimizaciones más comunes para el algoritmo de Bubble Sort es detener el proceso si en una iteración no se realizó ningún intercambio. Esto evita realizar pasadas innecesarias cuando el array ya está ordenado.
  
- **Reducción del rango de comparación**: En cada iteración del bucle externo, se reduce el rango del bucle interno ya que los últimos elementos estarán en su posición correcta. Por lo tanto, no es necesario seguir comparando los elementos ya ordenados.

### Complejidad:

- **Peor y promedio caso**: O(n²) - Si el array está desordenado.
- **Mejor caso**: O(n) - Si el array ya está ordenado (gracias al indicador `swapped`).

Este algoritmo es simple, pero es ineficiente para arrays grandes debido a su complejidad O(n²). Sin embargo, es útil para aprender el concepto de intercambios y bucles anidados.