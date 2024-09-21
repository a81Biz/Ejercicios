Aquí tienes los códigos en C# y JavaScript para invertir una cadena de texto, utilizando tanto métodos nativos como bucles. A continuación, te explico cómo funcionan cada uno:

### C# - Invertir una cadena

**Usando métodos nativos (`System.Linq`):**

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        string input = "Hola Mundo";
        string reversed = new string(input.Reverse().ToArray());
        Console.WriteLine(reversed); // Salida: odnuM aloH
    }
}
```

**Explicación:**
1. **`input.Reverse()`**: Usa el método `Reverse` de LINQ para invertir los caracteres de la cadena.
2. **`ToArray()`**: Convierte el resultado invertido a un arreglo de caracteres.
3. **`new string()`**: Crea una nueva cadena a partir del arreglo de caracteres.

**Usando un bucle:**

```csharp
using System;

class Program
{
    static void Main()
    {
        string input = "Hola Mundo";
        char[] charArray = input.ToCharArray();
        string reversed = "";

        for (int i = charArray.Length - 1; i >= 0; i--)
        {
            reversed += charArray[i];
        }

        Console.WriteLine(reversed); // Salida: odnuM aloH
    }
}
```

**Explicación:**
1. Convierte la cadena a un arreglo de caracteres con `ToCharArray()`.
2. Usa un bucle `for` que recorre el arreglo desde el último elemento hasta el primero.
3. Concatenar los caracteres en orden inverso a una nueva cadena.

### JavaScript - Invertir una cadena

**Usando métodos nativos (`split`, `reverse`, `join`):**

```javascript
const input = "Hola Mundo";
const reversed = input.split('').reverse().join('');
console.log(reversed); // Salida: odnuM aloH
```

**Explicación:**
1. **`split('')`**: Divide la cadena en un arreglo de caracteres.
2. **`reverse()`**: Invierte el orden del arreglo.
3. **`join('')`**: Une los caracteres del arreglo en una nueva cadena.

**Usando un bucle:**

```javascript
const input = "Hola Mundo";
let reversed = '';

for (let i = input.length - 1; i >= 0; i--) {
    reversed += input[i];
}

console.log(reversed); // Salida: odnuM aloH
```

**Explicación:**
1. Usa un bucle `for` que recorre la cadena desde el último carácter hasta el primero.
2. Concatena cada carácter en orden inverso a una nueva cadena.

Ambos enfoques, tanto en C# como en JavaScript, muestran cómo utilizar métodos nativos y bucles para invertir una cadena de manera eficiente y comprensible.