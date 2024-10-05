Aquí tienes las soluciones para encontrar el mínimo y máximo de un array en C# y JavaScript, utilizando tanto bucles simples como funciones nativas como `Math.Min` y `Math.Max`.

### C# - Usando un Bucle Simple

En este enfoque, recorremos el array una vez para encontrar el mínimo y máximo.

```csharp
public (int min, int max) FindMinMax(int[] nums) {
    if (nums == null || nums.Length == 0) {
        throw new ArgumentException("Array cannot be null or empty");
    }

    int min = nums[0];
    int max = nums[0];

    foreach (int num in nums) {
        if (num < min) {
            min = num;
        }
        if (num > max) {
            max = num;
        }
    }

    return (min, max);
}
```

### C# - Usando `Math.Min` y `Math.Max`

Aquí utilizamos las funciones nativas `Math.Min` y `Math.Max` para comparar elementos.

```csharp
public (int min, int max) FindMinMaxMath(int[] nums) {
    if (nums == null || nums.Length == 0) {
        throw new ArgumentException("Array cannot be null or empty");
    }

    int min = nums[0];
    int max = nums[0];

    foreach (int num in nums) {
        min = Math.Min(min, num);
        max = Math.Max(max, num);
    }

    return (min, max);
}
```

### JavaScript - Usando un Bucle Simple

El enfoque en JavaScript también utiliza un bucle simple para recorrer el array.

```javascript
function findMinMax(nums) {
    if (!nums.length) {
        throw new Error("Array cannot be empty");
    }

    let min = nums[0];
    let max = nums[0];

    for (let i = 1; i < nums.length; i++) {
        if (nums[i] < min) {
            min = nums[i];
        }
        if (nums[i] > max) {
            max = nums[i];
        }
    }

    return { min, max };
}
```

### JavaScript - Usando `Math.min` y `Math.max`

Con `Math.min` y `Math.max`, puedes encontrar el mínimo y máximo directamente utilizando el operador spread (`...`).

```javascript
function findMinMaxMath(nums) {
    if (!nums.length) {
        throw new Error("Array cannot be empty");
    }

    const min = Math.min(...nums);
    const max = Math.max(...nums);

    return { min, max };
}
```

### Explicación:

1. **Bucle Simple**:
   - Recorre el array una vez, comparando cada elemento con el mínimo y máximo actual.
   - Este enfoque tiene una complejidad de tiempo O(n).

2. **Funciones Nativas (`Math.Min/Max`)**:
   - Utiliza las funciones nativas `Math.min` y `Math.max` junto con el operador spread en JavaScript para encontrar el mínimo y máximo en una línea de código.
   - Aunque es más limpio, el uso de `Math.min` y `Math.max` con un array grande puede tener una mayor sobrecarga en JavaScript debido a la expansión del array.

Ambos enfoques son eficientes y útiles dependiendo del escenario.