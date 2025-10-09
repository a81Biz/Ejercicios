# 🔠 **LeetCode #3 — Longest Substring Without Repeating Characters**

> **Tema:** Sliding Window / HashMap
> **Nivel:** 🟡 Medio
> **Patrón:** *Two Pointers (ventana dinámica)*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Identificar qué mide la longitud y cómo controlar duplicados eficientemente.

**📖 Enunciado resumido:**
Dada una cadena `s`, devuelve la **longitud de la subcadena más larga sin caracteres repetidos.**

**🔢 Ejemplo de entrada:**

```
s = "abcabcbb"
```

**🎯 Salida esperada:**

```
3
```

(Subcadena más larga sin repetición: `"abc"`)

**💬 Reexplicación en voz alta:**

> “Debo recorrer la cadena y mantener una ventana que contenga solo caracteres únicos.
> Cuando aparece un duplicado, deslizo el inicio de la ventana para eliminarlo.”

**❓ Preguntas al entrevistador:**

* ¿Qué significa “subcadena”? → Secuencia continua de caracteres.
* ¿Importa si hay mayúsculas/minúsculas? → ✅ Sí, se consideran diferentes.
* ¿Cadena vacía? → Resultado `0`.
* ¿Qué pasa con caracteres especiales o espacios? → Se tratan igual que cualquier carácter.

**🧩 Casos límite:**

* [x] `s = ""` → `0`
* [x] `s = "bbbb"` → `1`
* [x] `s = "pwwkew"` → `3` (`"wke"`)
* [x] `s = "aab"` → `2`
* [x] `s = "dvdf"` → `3`

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Comprender cómo mantener el rango activo de la subcadena y detectar repeticiones.

**📚 Tipo de problema:**
➡️ Detección de secuencias únicas en una cadena → *Sliding Window / Two Pointers*.

---

### 💡 Enfoque 1 — Sliding Window con HashSet

**Idea:**

* Usar dos punteros (`left` y `right`) que delimitan la ventana activa.
* Un conjunto (`set`) guarda los caracteres actuales de la ventana.
* Avanzar `right` y agregar caracteres hasta encontrar un duplicado.
* Cuando hay duplicado, mover `left` hasta eliminarlo.
* Actualizar la longitud máxima en cada paso.

**Pseudocódigo:**

```
set = {}
left = 0
maxLen = 0

para right en [0..n):
    mientras s[right] en set:
        eliminar s[left]
        left++
    agregar s[right] al set
    maxLen = max(maxLen, right - left + 1)
return maxLen
```

**Complejidad:**
⏱️ Tiempo: `O(N)` — cada carácter se agrega y elimina como máximo una vez.
💾 Espacio: `O(K)` — K = tamaño del alfabeto (máx. 128 o 256).

---

### ⚙️ Enfoque 2 — HashMap (índice del último carácter visto)

**Idea:**
Guardar en un `Map` el índice más reciente de cada carácter.
Si un carácter repetido aparece dentro de la ventana, mover `left` justo después de su última aparición.

**Pseudocódigo:**

```
map = {}
left = 0
maxLen = 0

para right en [0..n):
    si s[right] está en map:
        left = max(left, map[s[right]] + 1)
    map[s[right]] = right
    maxLen = max(maxLen, right - left + 1)
return maxLen
```

**Complejidad:**
⏱️ `O(N)` tiempo
💾 `O(K)` espacio
**Ventaja:** movimientos más grandes del puntero `left`.

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Mostrar claridad en el control de la ventana y actualización de índices.

**✍️ Implementación (JavaScript - HashMap optimizado):**

```js
var lengthOfLongestSubstring = function(s) {
    let map = new Map();
    let left = 0;
    let maxLen = 0;

    for (let right = 0; right < s.length; right++) {
        const char = s[right];

        if (map.has(char)) {
            // Mueve left solo si el duplicado está dentro de la ventana
            left = Math.max(left, map.get(char) + 1);
        }

        map.set(char, right);
        maxLen = Math.max(maxLen, right - left + 1);
    }

    return maxLen;
};
```

**🗣️ Explicación hablada:**

> “Uso un mapa para guardar el último índice donde apareció cada carácter.
> Cuando encuentro un duplicado, salto el inicio de la ventana justo después de su posición anterior.
> Esto evita retrocesos innecesarios.
> Luego actualizo el mapa y la longitud máxima.”

---

### 💡 Alternativa (Set + While):

```js
var lengthOfLongestSubstring = function(s) {
    let set = new Set();
    let left = 0, maxLen = 0;

    for (let right = 0; right < s.length; right++) {
        while (set.has(s[right])) {
            set.delete(s[left]);
            left++;
        }
        set.add(s[right]);
        maxLen = Math.max(maxLen, right - left + 1);
    }

    return maxLen;
};
```

> “Con un Set controlo duplicados directamente.
> Si hay uno repetido, elimino desde el lado izquierdo hasta que desaparezca,
> manteniendo siempre una ventana válida.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Confirmar detección de duplicados y actualización de ventana.

**Casos de prueba:**

| # | Entrada      | Salida esperada | Resultado |
| - | ------------ | --------------- | --------- |
| 1 | `"abcabcbb"` | `3` (`"abc"`)   | ✅         |
| 2 | `"bbbbb"`    | `1` (`"b"`)     | ✅         |
| 3 | `"pwwkew"`   | `3` (`"wke"`)   | ✅         |
| 4 | `""`         | `0`             | ✅         |
| 5 | `"dvdf"`     | `3` (`"vdf"`)   | ✅         |

**🧠 Simulación paso a paso (HashMap, `"abcabcbb"`):**

```
r=0, char=a, left=0 → max=1
r=1, char=b, left=0 → max=2
r=2, char=c, left=0 → max=3
r=3, char=a repetido → left=max(0,0+1)=1
r=4, char=b repetido → left=max(1,1+1)=2
r=5, char=c repetido → left=max(2,2+1)=3
r=6, char=b → maxLen=3
r=7, char=b → maxLen=3
Resultado final = 3
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                         |
| ---------------------- | --------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — cada carácter procesado una vez.       |
| 💾 **Espacio:**        | O(K) — mapa o conjunto de caracteres activos. |
| ⚡ **Escalabilidad:**   | Excelente hasta cadenas de 10⁵ caracteres.    |
| 🧩 **Tipo de patrón:** | Sliding Window (Two Pointers).                |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar comprensión del patrón y su reutilización.

**🔧 Posibles optimizaciones:**

* Usar arreglo de 128 posiciones (ASCII) en lugar de `Map` para mayor velocidad.
* Evitar `while` eliminando saltos múltiples (como en el enfoque `Map`).
* Aplicar este mismo patrón para problemas:

  * *Minimum Window Substring*
  * *Longest Repeating Character Replacement*
  * *Substring with K Distinct Characters*

**📚 Lecciones aprendidas:**

* “Sliding Window” es una técnica esencial para optimizar problemas lineales.
* El uso de `Map` o `Set` depende del tipo de salto (unitario o múltiple).
* Saber cuándo avanzar `left` es la clave del razonamiento lógico.

**✅ Conclusión final:**

> “Mantuve una ventana deslizante sin duplicados usando dos punteros.
> En cada paso actualicé los límites según el último carácter repetido.
> La solución es óptima en tiempo y espacio.”

---

📘 **Resumen final**

| Aspecto          | Valor                             |
| ---------------- | --------------------------------- |
| Patrón           | Sliding Window / Two Pointers     |
| Complejidad      | O(N) tiempo, O(K) espacio         |
| Palabra clave    | “Ventana dinámica sin duplicados” |
| Tipo de problema | Cadenas / Optimización lineal     |
| Nivel            | 🟡 Medio                          |

---

✅ Con esto, completamos el **Bloque de Algoritmos Nivel Medio (6–10):**

| #  | Ejercicio                           | Patrón Principal |
| -- | ----------------------------------- | ---------------- |
| 6  | Number of Islands                   | DFS/BFS en grid  |
| 7  | Permutations                        | Backtracking     |
| 8  | Binary Tree Level Order Traversal   | BFS              |
| 9  | Validate BST                        | DFS con límites  |
| 10 | Longest Substring Without Repeating | Sliding Window   |
