# 🧩 **LeetCode #46 — Permutations**

> **Tema:** Recursión / Backtracking
> **Nivel:** 🟡 Medio
> **Patrón:** *Backtracking con árbol de decisiones*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Identificar claramente el propósito y restricciones del problema.

**📖 Enunciado resumido:**
Dado un arreglo de números **únicos** `nums`, devuelve **todas las permutaciones posibles**.
Una permutación es un reordenamiento completo de los elementos.

**🔢 Ejemplo de entrada:**

```
nums = [1, 2, 3]
```

**🎯 Salida esperada:**

```
[
 [1,2,3],
 [1,3,2],
 [2,1,3],
 [2,3,1],
 [3,1,2],
 [3,2,1]
]
```

**💬 Reexplicación en voz alta:**

> “Debo generar todas las posibles combinaciones donde el orden importe y no se repitan los elementos.
> Esto se resuelve explorando todas las decisiones posibles en forma de árbol.”

**❓ Preguntas al entrevistador:**

* ¿Los elementos pueden repetirse? → ❌ No, son únicos.
* ¿El orden importa? → ✅ Sí.
* ¿Debo devolver las permutaciones en un orden específico? → ❌ No, cualquiera es válido.
* ¿Cuál es el tamaño máximo del arreglo? → Típicamente `n ≤ 6` (ya que la complejidad es factorial).

**🧩 Casos límite:**

* [x] `nums = []` → `[[]]`
* [x] `nums = [1]` → `[[1]]`
* [x] `nums = [1,2]` → `[[1,2],[2,1]]`

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Comprender la estructura recursiva y cómo evitar repeticiones.

**📚 Tipo de problema:**
➡️ Generación completa de combinaciones ordenadas → **Backtracking / Recursión con estado parcial**.

---

### 💡 Enfoque 1 — Backtracking con arreglo temporal y visitados

**Idea:**

* Explorar recursivamente cada posición.
* En cada nivel, probar cada número **no usado aún**.
* Cuando el arreglo temporal (`path`) alcanza longitud `n`, agregarlo al resultado.
* Retroceder (“backtrack”) para probar otras combinaciones.

**Pseudocódigo:**

```
resultado = []
path = []
visitados = []

func backtrack():
    si path.length == nums.length:
        resultado.push([...path])
        return

    para cada num en nums:
        si num ya usado → continuar
        marcar num como usado
        path.push(num)
        backtrack()
        path.pop()
        desmarcar num
```

**Complejidad:**
⏱️ Tiempo: `O(N × N!)` — N! permutaciones, cada una construida en O(N).
💾 Espacio: `O(N)` (profundidad de la recursión + estado temporal).

---

### ⚙️ Enfoque 2 — Intercambio In-Place

**Idea:**
Intercambiar elementos del arreglo original para generar todas las permutaciones sin usar estructuras adicionales.

**Pseudocódigo:**

```
func permute(start):
    si start == nums.length:
        resultado.push([...nums])
        return

    para i desde start hasta fin:
        swap(nums[start], nums[i])
        permute(start + 1)
        swap(nums[start], nums[i])
```

**Complejidad:**
Igual: `O(N × N!)` tiempo, `O(N)` espacio.
**Ventaja:** no requiere arreglo de “visitados”.

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Codificar el backtracking de forma clara y modular.

**✍️ Implementación (JavaScript - enfoque con `visitados`):**

```js
var permute = function(nums) {
    const result = [];
    const used = Array(nums.length).fill(false);

    const backtrack = (path) => {
        if (path.length === nums.length) {
            result.push([...path]);
            return;
        }

        for (let i = 0; i < nums.length; i++) {
            if (used[i]) continue; // evitar repetir
            used[i] = true;
            path.push(nums[i]);

            backtrack(path);

            path.pop(); // retroceder
            used[i] = false;
        }
    };

    backtrack([]);
    return result;
};
```

**🗣️ Explicación hablada:**

> “Uso un arreglo ‘used’ para marcar los elementos ya tomados.
> En cada llamada recursiva agrego uno nuevo y cuando alcanzo la longitud completa, guardo una copia.
> Luego retrocedo para probar el siguiente camino.
> Cada combinación única se genera así.”

---

### 💡 Alternativa (Intercambio in-place):

```js
var permute = function(nums) {
    const result = [];

    const backtrack = (start) => {
        if (start === nums.length) {
            result.push([...nums]);
            return;
        }

        for (let i = start; i < nums.length; i++) {
            [nums[start], nums[i]] = [nums[i], nums[start]];
            backtrack(start + 1);
            [nums[start], nums[i]] = [nums[i], nums[start]]; // revertir swap
        }
    };

    backtrack(0);
    return result;
};
```

> “Intercambio posiciones directamente en el arreglo.
> Cada posición ‘start’ fija un número, y los siguientes se permutan recursivamente.
> Al terminar, revierte el swap para restaurar el estado previo.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Verificar todas las permutaciones posibles y casos límite.

**Casos de prueba:**

| # | Entrada   | Salida esperada (orden no importa) | Resultado |
| - | --------- | ---------------------------------- | --------- |
| 1 | `[1,2,3]` | 6 permutaciones únicas             | ✅         |
| 2 | `[1,2]`   | `[[1,2],[2,1]]`                    | ✅         |
| 3 | `[1]`     | `[[1]]`                            | ✅         |
| 4 | `[]`      | `[[]]`                             | ✅         |
| 5 | `[0,1]`   | `[[0,1],[1,0]]`                    | ✅         |

**🧠 Simulación (para [1,2,3]):**

```
Nivel 0: []
 → +1 → [1]
    → +2 → [1,2]
       → +3 → [1,2,3] ✅
       ← backtrack
    → +3 → [1,3,2] ✅
 ← backtrack
 → +2 → [2,1,3], [2,3,1]
 → +3 → [3,1,2], [3,2,1]
```

Total: 6 permutaciones.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                       |
| ---------------------- | ------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N × N!)                                   |
| 💾 **Espacio:**        | O(N) (recursión + estado temporal)          |
| ⚡ **Escalabilidad:**   | Razonable hasta N ≈ 8                       |
| 🧩 **Tipo de patrón:** | Backtracking / Recursión con poda implícita |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar madurez en backtracking y variantes posibles.

**🔧 Posibles optimizaciones:**

* Si existieran duplicados → usar un Set o control adicional para evitar repeticiones.
* Usar el método in-place (`swap`) para reducir espacio adicional.
* Aplicar el mismo patrón para generar combinaciones, subsets o paths.

**📚 Lecciones aprendidas:**

* Backtracking = *explorar todas las decisiones posibles y retroceder después de cada intento*.
* La recursión forma un **árbol de decisión** donde cada hoja representa una solución válida.
* Entender el flujo de “agregar → explorar → eliminar” es esencial para todo tipo de combinatoria.

**✅ Conclusión final:**

> “Generé todas las permutaciones recorriendo un árbol de decisiones con backtracking.
> Cada nivel fija un número distinto hasta completar una combinación válida.
> Es un patrón esencial en entrevistas de algoritmos combinatorios.”

---

📘 **Resumen final**

| Aspecto          | Valor                             |
| ---------------- | --------------------------------- |
| Patrón           | Backtracking / Recursión          |
| Complejidad      | O(N×N!) tiempo, O(N) espacio      |
| Palabra clave    | “Explorar, retroceder, continuar” |
| Tipo de problema | Generación combinatoria           |
| Nivel            | 🟡 Medio                          |

