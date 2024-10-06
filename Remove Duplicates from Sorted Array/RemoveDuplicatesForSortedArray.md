A continuación te muestro cómo puedes eliminar duplicados en un array ordenado tanto en C# como en JavaScript, utilizando índices y comparaciones de valores consecutivos:

### C#

```csharp
public class RemoveDuplicates
{
    public static int RemoveDuplicatesFromSortedArray(int[] nums)
    {
        if (nums.Length == 0) return 0;

        int index = 1; // El índice donde guardaremos el siguiente valor único.

        for (int i = 1; i < nums.Length; i++)
        {
            // Comparamos el valor actual con el anterior.
            if (nums[i] != nums[i - 1])
            {
                nums[index] = nums[i]; // Guardamos el valor único.
                index++; // Incrementamos el índice.
            }
        }

        return index; // Devuelve la longitud del array sin duplicados.
    }

    public static void Main()
    {
        int[] nums = {1, 1, 2, 2, 3, 3, 4};
        int newLength = RemoveDuplicatesFromSortedArray(nums);
        
        for (int i = 0; i < newLength; i++)
        {
            Console.Write(nums[i] + " ");
        }
    }
}
```

### Explicación:
- El array está ordenado, por lo que podemos comparar cada valor con su consecutivo.
- Si el valor en `i` es diferente del valor en `i-1`, significa que es un valor único.
- Mantenemos un índice `index` para guardar los valores únicos sin sobrescribir los duplicados.
- Al final, el valor de `index` será la longitud del array sin duplicados.

### JavaScript

```javascript
function removeDuplicatesFromSortedArray(nums) {
    if (nums.length === 0) return 0;

    let index = 1; // El índice para almacenar el siguiente valor único.

    for (let i = 1; i < nums.length; i++) {
        // Comparamos el valor actual con el anterior.
        if (nums[i] !== nums[i - 1]) {
            nums[index] = nums[i]; // Guardamos el valor único.
            index++; // Incrementamos el índice.
        }
    }

    return index; // Devuelve la longitud del array sin duplicados.
}

// Ejemplo de uso:
let nums = [1, 1, 2, 2, 3, 3, 4];
let newLength = removeDuplicatesFromSortedArray(nums);

console.log(nums.slice(0, newLength)); // Muestra el array sin duplicados.
```

### Explicación:
- Igual que en el código de C#, comparamos valores consecutivos y guardamos los valores únicos en la posición indicada por el índice `index`.
- Usamos `nums.slice(0, newLength)` para imprimir solo la parte del array que contiene los valores únicos.

Ambos códigos eliminan duplicados de un array ordenado de manera eficiente, manteniendo el espacio extra en O(1) y la complejidad de tiempo en O(n).