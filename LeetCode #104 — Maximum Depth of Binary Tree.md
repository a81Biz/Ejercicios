
# 🌳 **LeetCode #104 — Maximum Depth of Binary Tree**

> **Tema:** DFS Recursivo / Árboles Binarios
> **Nivel:** 🟢 Fácil
> **Patrón:** *Depth-First Search (Recursión)*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Asegurar el entendimiento completo antes de escribir código.

**📖 Enunciado resumido:**
Dado el nodo raíz de un árbol binario, devuelve su **profundidad máxima**.
La profundidad se define como **el número de nodos en el camino más largo desde la raíz hasta una hoja**.

**🔢 Ejemplo de entrada (visual):**

```
    3
   / \
  9  20
     / \
    15  7
```

**🎯 Salida esperada:**
`3`
(Camino más profundo: 3 → 20 → 15 o 3 → 20 → 7)

**💬 Reexplicación en voz alta:**

> “Debo calcular el nivel más profundo del árbol.
> Si no hay nodos, la profundidad es 0.
> Si solo hay la raíz, la profundidad es 1.”

**❓ Preguntas al entrevistador:**

* ¿Qué pasa si el árbol está vacío? → Devolver `0`.
* ¿Se considera un árbol con un solo nodo como profundidad 1? → ✅ Sí.
* ¿El árbol siempre es binario? → ✅ Sí. Cada nodo tiene como máximo dos hijos (`left`, `right`).
* ¿Importa el orden de los hijos? → ❌ No, solo la profundidad máxima.

**🧩 Casos límite:**

* [x] `root = null` → salida `0`
* [x] `root = [1]` → salida `1`
* [x] Árbol completamente balanceado.
* [x] Árbol completamente inclinado (todos a la izquierda o derecha).

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Mostrar razonamiento estructurado y patrones de exploración.

**📚 Tipo de problema:**
➡️ Recorrido y conteo de profundidad en estructura jerárquica → **DFS / Recursión.**

---

### 💡 Enfoque 1 — DFS Recursivo

**Idea:**
Calcular la profundidad del subárbol izquierdo y derecho recursivamente,
y tomar el máximo entre ambos + 1 (por el nodo actual).

**Fórmula:**

```
depth(node) = 1 + max(depth(node.left), depth(node.right))
```

**Caso base:**
Si `node == null` → profundidad `0`.

**Complejidad:**
⏱️ Tiempo: `O(N)` (visitamos cada nodo una vez).
💾 Espacio: `O(H)` donde `H` es la altura del árbol (por la pila de recursión).

---

### ⚙️ Enfoque 2 — BFS Iterativo (Cola)

**Idea:**
Recorrer el árbol por niveles con una cola,
aumentando la profundidad cada vez que terminamos un nivel.

**Complejidad:**
⏱️ Tiempo: `O(N)`
💾 Espacio: `O(W)` (W = anchura máxima del árbol).

---

**🗺️ Plan de pasos (pseudocódigo - DFS):**

```
func maxDepth(node):
    si node es null → return 0
    izq = maxDepth(node.left)
    der = maxDepth(node.right)
    return 1 + max(izq, der)
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Codificar con claridad, explicando cada decisión.

**✍️ Implementación (JavaScript):**

```js
var maxDepth = function(root) {
    if (!root) return 0; // caso base

    const leftDepth = maxDepth(root.left);
    const rightDepth = maxDepth(root.right);

    return 1 + Math.max(leftDepth, rightDepth);
};
```

**🗣️ Explicación hablada:**

> “Si el nodo es nulo, la profundidad es 0.
> Para cualquier otro nodo, calculo la profundidad de su hijo izquierdo y derecho.
> Tomo el máximo y le sumo 1 (por el nodo actual).
> Así, la recursión escala desde las hojas hacia arriba.”

---

### 💡 Alternativa (BFS iterativo)

```js
var maxDepth = function(root) {
    if (!root) return 0;
    let depth = 0;
    const queue = [root];

    while (queue.length > 0) {
        const size = queue.length;
        for (let i = 0; i < size; i++) {
            const node = queue.shift();
            if (node.left) queue.push(node.left);
            if (node.right) queue.push(node.right);
        }
        depth++;
    }

    return depth;
};
```

> “Cada vez que completo un nivel, aumento la variable `depth`.
> Cuando la cola se vacía, he recorrido todos los niveles del árbol.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Probar diferentes configuraciones de árboles.

**Casos de prueba:**

| # | Árbol (entrada)            | Salida esperada | Resultado |
| - | -------------------------- | --------------- | --------- |
| 1 | `[]`                       | `0`             | ✅         |
| 2 | `[1]`                      | `1`             | ✅         |
| 3 | `[3,9,20,null,null,15,7]`  | `3`             | ✅         |
| 4 | `[1,2,null,3,null,4,null]` | `4`             | ✅         |
| 5 | `[1,2,3,4,5]`              | `3`             | ✅         |

**🧠 Simulación (DFS):**

```
Nodo(3)
 ├─ maxDepth(9) = 1
 └─ maxDepth(20)
      ├─ maxDepth(15) = 1
      └─ maxDepth(7) = 1
 → return 1 + max(1, 2) = 3
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                   |
| ---------------------- | --------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — cada nodo visitado una vez.      |
| 💾 **Espacio:**        | O(H) — altura del árbol (recursión).    |
| ⚡ **Escalabilidad:**   | Ideal para árboles de hasta ~10⁴ nodos. |
| 🧩 **Tipo de patrón:** | DFS recursivo, recursión postorden.     |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar madurez técnica y variantes posibles.

**🔧 Posibles optimizaciones:**

* Cambiar a BFS iterativo para evitar desbordamiento de pila en árboles muy profundos.
* Implementar versión tail-recursive (si el lenguaje lo soporta).
* Añadir memoización si el árbol fuera reutilizado.

**📚 Lecciones aprendidas:**

* Este es un patrón clásico de **divide y vencerás** aplicado a árboles.
* La recursión es natural en árboles, pero conviene pensar también en versiones iterativas.
* Saber transformar un DFS recursivo en BFS iterativo demuestra madurez técnica.

**✅ Conclusión final:**

> “He recorrido el árbol calculando la profundidad máxima usando DFS.
> La solución es clara, O(N) en tiempo y O(H) en espacio, explicable fácilmente en pizarra.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | DFS (Depth-First Search)               |
| Complejidad      | O(N) tiempo, O(H) espacio              |
| Palabra clave    | “Altura del árbol = 1 + max(izq, der)” |
| Tipo de problema | Árbol Binario / Recursión              |
| Nivel            | 🟢 Fácil                               |

---