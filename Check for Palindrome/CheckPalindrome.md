Aquí tienes los códigos en C# y JavaScript para verificar si una cadena es un palíndromo, utilizando tanto métodos de inversión como comparaciones de substrings:

### C# - Verificar Palíndromos

**Usando métodos de inversión:**

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        string input = "Anita lava la tina";
        bool isPalindrome = IsPalindrome(input);
        Console.WriteLine(isPalindrome ? "Es un palíndromo" : "No es un palíndromo");
    }

    static bool IsPalindrome(string input)
    {
        // Remover espacios y convertir a minúsculas
        string processedInput = new string(input.Where(char.IsLetterOrDigit).ToArray()).ToLower();
        // Invertir la cadena
        string reversed = new string(processedInput.Reverse().ToArray());
        // Comparar la cadena original con la invertida
        return processedInput == reversed;
    }
}
```

**Explicación:**
1. **Remoción de caracteres no alfanuméricos y conversión a minúsculas**: Se utiliza `Where(char.IsLetterOrDigit)` y `ToLower()` para eliminar espacios y caracteres especiales, dejando solo letras y números en minúsculas.
2. **Inversión de la cadena**: Se usa `Reverse()` y `ToArray()` para invertir la cadena.
3. **Comparación**: Se compara la cadena original procesada con la invertida.

**Usando comparación de substrings:**

```csharp
using System;

class Program
{
    static void Main()
    {
        string input = "Anita lava la tina";
        bool isPalindrome = IsPalindrome(input);
        Console.WriteLine(isPalindrome ? "Es un palíndromo" : "No es un palíndromo");
    }

    static bool IsPalindrome(string input)
    {
        string processedInput = new string(input.Where(char.IsLetterOrDigit).ToArray()).ToLower();
        int length = processedInput.Length;

        for (int i = 0; i < length / 2; i++)
        {
            if (processedInput[i] != processedInput[length - 1 - i])
                return false;
        }
        return true;
    }
}
```

**Explicación:**
1. Se procesa la cadena para eliminar espacios y caracteres no alfanuméricos, similar al método anterior.
2. Usa un bucle `for` para comparar los caracteres desde los extremos hacia el centro.

### JavaScript - Verificar Palíndromos

**Usando métodos de inversión:**

```javascript
function isPalindrome(input) {
    // Remover espacios y convertir a minúsculas
    const processedInput = input.toLowerCase().replace(/[^a-z0-9]/g, '');
    // Invertir la cadena
    const reversed = processedInput.split('').reverse().join('');
    // Comparar la cadena original con la invertida
    return processedInput === reversed;
}

const input = "Anita lava la tina";
console.log(isPalindrome(input) ? "Es un palíndromo" : "No es un palíndromo");
```

**Explicación:**
1. **`toLowerCase().replace(/[^a-z0-9]/g, '')`**: Convierte la cadena a minúsculas y elimina caracteres no alfanuméricos.
2. **`split('').reverse().join('')`**: Invierte la cadena y la convierte nuevamente a una cadena.
3. **Comparación**: Se verifica si la cadena procesada es igual a la invertida.

**Usando comparación de substrings:**

```javascript
function isPalindrome(input) {
    const processedInput = input.toLowerCase().replace(/[^a-z0-9]/g, '');
    const length = processedInput.length;

    for (let i = 0; i < length / 2; i++) {
        if (processedInput[i] !== processedInput[length - 1 - i]) {
            return false;
        }
    }
    return true;
}

const input = "Anita lava la tina";
console.log(isPalindrome(input) ? "Es un palíndromo" : "No es un palíndromo");
```

**Explicación:**
1. Procesa la cadena eliminando caracteres no alfanuméricos y convirtiéndola a minúsculas.
2. Usa un bucle para comparar caracteres desde los extremos hacia el centro.

Ambos enfoques demuestran cómo verificar palíndromos utilizando métodos de inversión y comparaciones directas de substrings, tanto en C# como en JavaScript.