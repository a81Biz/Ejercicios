Aquí tienes el código para verificar si dos cadenas son anagramas en C# y JavaScript, utilizando arreglos y funciones de ordenamiento:

### C# - Verificar Anagramas

**Usando arreglos y ordenamiento:**

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        string str1 = "Listen";
        string str2 = "Silent";
        bool areAnagrams = AreAnagrams(str1, str2);
        Console.WriteLine(areAnagrams ? "Son anagramas" : "No son anagramas");
    }

    static bool AreAnagrams(string str1, string str2)
    {
        // Remover espacios, convertir a minúsculas y ordenar caracteres
        char[] arr1 = str1.ToLower().Where(char.IsLetterOrDigit).ToArray();
        char[] arr2 = str2.ToLower().Where(char.IsLetterOrDigit).ToArray();

        // Ordenar los arreglos
        Array.Sort(arr1);
        Array.Sort(arr2);

        // Comparar los arreglos ordenados
        return new string(arr1) == new string(arr2);
    }
}
```

**Explicación:**
1. **Procesamiento de cadenas**: Convierte las cadenas a minúsculas, elimina espacios y caracteres no alfanuméricos.
2. **Ordenamiento**: Usa `Array.Sort()` para ordenar los caracteres de ambas cadenas.
3. **Comparación**: Convierte los arreglos ordenados nuevamente a cadenas y compara si son iguales.

### JavaScript - Verificar Anagramas

**Usando arreglos y ordenamiento:**

```javascript
function areAnagrams(str1, str2) {
    // Remover espacios, convertir a minúsculas y ordenar caracteres
    const processString = str => str.toLowerCase().replace(/[^a-z0-9]/g, '').split('').sort().join('');
    
    // Procesar y comparar las cadenas
    return processString(str1) === processString(str2);
}

const str1 = "Listen";
const str2 = "Silent";
console.log(areAnagrams(str1, str2) ? "Son anagramas" : "No son anagramas");
```

**Explicación:**
1. **`processString`**: Función que convierte la cadena a minúsculas, elimina caracteres no alfanuméricos, divide en un arreglo de caracteres, ordena y une nuevamente en una cadena.
2. **Comparación**: Compara las cadenas procesadas para verificar si son iguales.

Ambos métodos utilizan el mismo enfoque lógico: limpiar las cadenas, ordenarlas y luego compararlas. Esto asegura que dos cadenas tengan los mismos caracteres en la misma cantidad y orden, lo cual define que son anagramas.