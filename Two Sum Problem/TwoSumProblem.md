Aquí te proporciono dos soluciones para el problema "Two Sum", una utilizando un enfoque de bucle doble y otra optimizada con un hashmap, tanto en C# como en JavaScript.

### Enfoque de Bucle Doble

Este enfoque itera sobre todos los pares posibles en el array para encontrar la solución. Tiene una complejidad de tiempo O(n²).

#### C# - Bucle Doble
```csharp
public int[] TwoSum(int[] nums, int target) {
    for (int i = 0; i < nums.Length - 1; i++) {
        for (int j = i + 1; j < nums.Length; j++) {
            if (nums[i] + nums[j] == target) {
                return new int[] { i, j };
            }
        }
    }
    throw new ArgumentException("No two sum solution");
}
```

#### JavaScript - Bucle Doble
```javascript
function twoSum(nums, target) {
    for (let i = 0; i < nums.length - 1; i++) {
        for (let j = i + 1; j < nums.length; j++) {
            if (nums[i] + nums[j] === target) {
                return [i, j];
            }
        }
    }
    throw new Error("No two sum solution");
}
```

### Enfoque Optimizado con Hashmap

Este enfoque utiliza un hashmap (o diccionario) para almacenar los valores del array mientras los recorremos una sola vez, logrando una complejidad de tiempo O(n).

#### C# - Hashmap
```csharp
public int[] TwoSum(int[] nums, int target) {
    Dictionary<int, int> map = new Dictionary<int, int>();
    for (int i = 0; i < nums.Length; i++) {
        int complement = target - nums[i];
        if (map.ContainsKey(complement)) {
            return new int[] { map[complement], i };
        }
        map[nums[i]] = i;
    }
    throw new ArgumentException("No two sum solution");
}
```

#### JavaScript - Hashmap
```javascript
function twoSum(nums, target) {
    let map = new Map();
    for (let i = 0; i < nums.length; i++) {
        let complement = target - nums[i];
        if (map.has(complement)) {
            return [map.get(complement), i];
        }
        map.set(nums[i], i);
    }
    throw new Error("No two sum solution");
}
```

### Explicación:

- **Bucle Doble**: El algoritmo compara todos los pares posibles de elementos dentro del array hasta encontrar la suma correcta.
- **Hashmap**: El algoritmo recorre el array solo una vez. Mientras lo hace, busca si el complemento de cada número (es decir, la diferencia entre el objetivo y el número actual) ya está en el hashmap. Si está, se devuelve el índice del complemento y el índice actual. Si no está, se añade el número actual y su índice al hashmap.

Ambos enfoques resuelven el problema, pero el uso del hashmap es más eficiente, especialmente con grandes cantidades de datos.