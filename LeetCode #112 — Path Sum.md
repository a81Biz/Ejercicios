# 🌳 **LeetCode #112 — Path Sum**

> **Tema:** Árboles Binarios / DFS con Acumulación
> **Nivel:** 🟢 Fácil
> **Patrón:** *Depth-First Search con condición de suma*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender completamente qué pregunta el problema antes de programar.

**📖 Enunciado resumido:**
Dado el nodo raíz de un árbol binario y un número entero `targetSum`,
determina si **existe al menos un camino desde la raíz hasta una hoja** tal que la **suma de los valores de los nodos** a lo largo del camino sea igual a `targetSum`.

**🔢 Ejemplo de entrada:**

```
root = [5,4,8,11,null,13,4,7,2,null,null,null,1]
targetSum = 22
```

**🎯 Salida esperada:**

```
true
```

**💬 Reexplicación en voz alta:**

> “Busco un camino desde la raíz hasta alguna hoja en el que la suma acumulada de los valores sea igual al número objetivo.
> No necesito todos los caminos, solo verificar si existe uno.”

**❓ Preguntas al entrevistador:**

* ¿Un camino debe terminar en una hoja? → ✅ Sí, solo cuentan los que llegan a una hoja.
* ¿Puede haber valores negativos? → ✅ Sí.
* ¿Qué pasa si el árbol está vacío? → Devuelvo `false`.
* ¿Puede haber múltiples caminos válidos? → ✅ Sí, basta con que exista uno.

**🧩 Casos límite:**

* [x] `root = null` → `false`
* [x] `root = [1,2,3]`, `targetSum = 5` → `false`
* [x] `root = [1,2,3]`, `targetSum = 3` → `true` (1→2)
* [x] Árbol con valores negativos.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Visualizar cómo recorrer el árbol y acumular valores.

**📚 Tipo de problema:**
➡️ Recorrido condicional de árbol binario → **DFS con acumulación (top-down).**

---

### 💡 Enfoque 1 — DFS Recursivo (Top-Down)

**Idea:**
Recorrer el árbol desde la raíz hasta las hojas,
restando el valor del nodo actual al `targetSum` en cada paso.
Si llegamos a una hoja y `targetSum - node.val === 0`, el camino cumple la condición.

**Pseudocódigo:**

```
func hasPathSum(node, sum):
    si node == null → return false

    si node es hoja → return (sum - node.val == 0)

    return hasPathSum(node.left, sum - node.val)
        || hasPathSum(node.right, sum - node.val)
```

**Complejidad:**
⏱️ Tiempo: `O(N)` — cada nodo se visita una vez.
💾 Espacio: `O(H)` — altura del árbol por recursión.

---

### ⚙️ Enfoque 2 — DFS Iterativo (Pila)

**Idea:**
Usar una pila con pares `(nodo, sumaAcumulada)` y recorrer manualmente.

**Pseudocódigo:**

```
stack = [(root, root.val)]
mientras stack no vacía:
    node, currentSum = stack.pop()
    si node es hoja y currentSum == targetSum → return true
    si node.right → stack.push(node.right, currentSum + node.right.val)
    si node.left → stack.push(node.left, currentSum + node.left.val)
return false
```

**Complejidad:**
Similar: O(N) tiempo, O(H) espacio.

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Explicar el proceso recursivo con claridad.

**✍️ Implementación (JavaScript - DFS Recursivo):**

```js
var hasPathSum = function(root, targetSum) {
    if (!root) return false; // Árbol vacío

    // Si llegamos a una hoja, verificamos si se cumple la suma exacta
    if (!root.left && !root.right) {
        return targetSum === root.val;
    }

    // Continuar el recorrido en subárboles
    const newTarget = targetSum - root.val;
    return hasPathSum(root.left, newTarget) || hasPathSum(root.right, newTarget);
};
```

**🗣️ Explicación hablada:**

> “En cada paso resto el valor del nodo actual al objetivo.
> Si llego a una hoja y el resultado es 0, encontré un camino válido.
> La recursión explora todos los caminos posibles hasta que encuentra uno que cumple la condición.”

---

### 💡 Alternativa (Iterativa con pila):

```js
var hasPathSum = function(root, targetSum) {
    if (!root) return false;
    const stack = [[root, root.val]];

    while (stack.length > 0) {
        const [node, sum] = stack.pop();

        if (!node.left && !node.right && sum === targetSum) return true;

        if (node.right) stack.push([node.right, sum + node.right.val]);
        if (node.left) stack.push([node.left, sum + node.left.val]);
    }

    return false;
};
```

> “Mantengo una pila con el nodo y la suma acumulada.
> Si encuentro una hoja con suma igual al objetivo, detengo la búsqueda.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Confirmar que se detectan correctamente los caminos válidos.

**Casos de prueba:**

| # | Árbol (entrada)                             | `targetSum` | Resultado esperado | Resultado |
| - | ------------------------------------------- | ----------- | ------------------ | --------- |
| 1 | `[5,4,8,11,null,13,4,7,2,null,null,null,1]` | `22`        | `true`             | ✅         |
| 2 | `[1,2,3]`                                   | `5`         | `false`            | ✅         |
| 3 | `[1,2,3]`                                   | `3`         | `true`             | ✅         |
| 4 | `[]`                                        | `0`         | `false`            | ✅         |
| 5 | `[1,2]`                                     | `0`         | `false`            | ✅         |

**🧠 Simulación (DFS con targetSum = 22):**

```
5 → newTarget = 17
 → 4 → newTarget = 13
   → 11 → newTarget = 2
     → 7 → newTarget = -5 ❌
     → 2 → newTarget = 0 ✅ → camino válido encontrado
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                         |
| ---------------------- | --------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — se recorre cada nodo una vez.          |
| 💾 **Espacio:**        | O(H) — altura del árbol (recursión).          |
| 🧩 **Tipo de patrón:** | DFS con acumulación descendente.              |
| ⚡ **Escalabilidad:**   | Muy buena para árboles medianos (~10⁴ nodos). |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar análisis crítico y posibles extensiones.

**🔧 Posibles optimizaciones:**

* Podar ramas en cuanto la suma parcial supere el objetivo (si todos los valores son positivos).
* Convertir en BFS para encontrar caminos mínimos si se extiende a pesos negativos.
* Guardar rutas completas si el requerimiento cambiara a "listar todos los caminos válidos".

**📚 Lecciones aprendidas:**

* Patrón clásico de DFS donde se acumula un valor en el camino.
* Recursión top-down con actualización del parámetro es más clara que usar una variable global.
* Los árboles son ideales para practicar *backtracking con condiciones*.

**✅ Conclusión final:**

> “Recorrí el árbol restando el valor de cada nodo al objetivo.
> Si llego a una hoja con suma cero, existe un camino válido.
> Es una aplicación directa de DFS con acumulación de estado.”

---

📘 **Resumen final**

| Aspecto          | Valor                               |
| ---------------- | ----------------------------------- |
| Patrón           | DFS Recursivo con acumulación       |
| Complejidad      | O(N) tiempo, O(H) espacio           |
| Palabra clave    | “Resta progresiva del targetSum”    |
| Tipo de problema | Árbol Binario / Camino condicionado |
| Nivel            | 🟢 Fácil                            |

---

✅ Con esta ficha completamos el bloque **Algoritmos Nivel Fácil (1–5)**:
1️⃣ Climbing Stairs
2️⃣ Maximum Depth of Binary Tree
3️⃣ Merge Two Sorted Lists
4️⃣ Invert Binary Tree
5️⃣ Path Sum
