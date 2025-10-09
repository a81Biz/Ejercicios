# 🌲 **LeetCode #102 — Binary Tree Level Order Traversal**

> **Tema:** Árboles Binarios / BFS
> **Nivel:** 🟡 Medio
> **Patrón:** *Breadth-First Search (Recorrido por Niveles)*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender la estructura del recorrido y el formato esperado de salida.

**📖 Enunciado resumido:**
Dado el nodo raíz de un árbol binario, devuelve una **lista de listas**,
donde cada lista contiene los valores de los nodos en cada nivel del árbol.

**🔢 Ejemplo de entrada:**

```
    3
   / \
  9  20
     / \
    15  7
```

**🎯 Salida esperada:**

```
[
 [3],
 [9,20],
 [15,7]
]
```

**💬 Reexplicación en voz alta:**

> “Debo recorrer el árbol **nivel por nivel**, de arriba hacia abajo,
> agrupando los nodos que estén en el mismo nivel.”

**❓ Preguntas al entrevistador:**

* ¿El árbol puede estar vacío? → ✅ Sí, devolver `[]`.
* ¿Se requiere un orden específico (izquierda a derecha)? → ✅ Sí.
* ¿Debo devolver los valores o los nodos completos? → Solo los valores (`val`).
* ¿Se debe usar BFS o DFS? → Cualquiera sirve, pero BFS es más natural aquí.

**🧩 Casos límite:**

* [x] Árbol vacío → `[]`
* [x] Árbol con un solo nodo → `[[root.val]]`
* [x] Árbol perfectamente balanceado
* [x] Árbol desbalanceado (solo ramas izquierdas o derechas)

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Decidir la estrategia de recorrido más clara y estructurada.

**📚 Tipo de problema:**
➡️ Recorrido de árbol por niveles → **BFS (Breadth-First Search)** usando una cola (FIFO).

---

### 💡 Enfoque 1 — BFS Iterativo

**Idea:**

* Usar una **cola (`queue`)** para mantener los nodos del nivel actual.
* En cada iteración:

  * Procesar todos los nodos del nivel (usando `size = queue.length`).
  * Agregar sus valores a una lista temporal.
  * Encolar sus hijos (si existen).
* Al final de cada nivel, agregar la lista temporal al resultado final.

**Pseudocódigo:**

```
resultado = []
queue = [root]

mientras queue no vacía:
    nivel = []
    size = queue.length
    repetir size veces:
        node = queue.pop()
        nivel.push(node.val)
        si node.left → queue.push(node.left)
        si node.right → queue.push(node.right)
    resultado.push(nivel)
return resultado
```

**Complejidad:**
⏱️ Tiempo: `O(N)` — cada nodo visitado una vez.
💾 Espacio: `O(W)` — W = número máximo de nodos por nivel.

---

### ⚙️ Enfoque 2 — DFS con nivel explícito

**Idea:**
Usar una función recursiva `dfs(node, level)` que agregue los valores en la posición `level` del resultado.

**Pseudocódigo:**

```
func dfs(node, nivel):
    si node es null → return
    si resultado[nivel] no existe → resultado[nivel] = []
    resultado[nivel].push(node.val)
    dfs(node.left, nivel+1)
    dfs(node.right, nivel+1)
```

**Complejidad:**
⏱️ Tiempo: `O(N)`
💾 Espacio: `O(H)` (altura del árbol).

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar BFS con código legible y explicar cada decisión.

**✍️ Implementación (JavaScript - BFS iterativo):**

```js
var levelOrder = function(root) {
    if (!root) return [];

    const result = [];
    const queue = [root];

    while (queue.length > 0) {
        const levelSize = queue.length;
        const levelNodes = [];

        for (let i = 0; i < levelSize; i++) {
            const node = queue.shift();
            levelNodes.push(node.val);

            if (node.left) queue.push(node.left);
            if (node.right) queue.push(node.right);
        }

        result.push(levelNodes);
    }

    return result;
};
```

**🗣️ Explicación hablada:**

> “Uso una cola que almacena los nodos pendientes por nivel.
> Tomo el tamaño del nivel actual para no mezclar nodos de diferentes niveles.
> Extraigo todos los nodos del nivel, guardo sus valores y encolo sus hijos.
> Repetir hasta vaciar la cola.”

---

### 💡 Alternativa (DFS Recursivo):

```js
var levelOrder = function(root) {
    const result = [];

    const dfs = (node, level) => {
        if (!node) return;
        if (!result[level]) result[level] = [];
        result[level].push(node.val);

        dfs(node.left, level + 1);
        dfs(node.right, level + 1);
    };

    dfs(root, 0);
    return result;
};
```

> “DFS también puede generar los niveles si pasamos el índice `level`
> y agregamos los valores en la posición correspondiente del arreglo resultado.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Comprobar el recorrido correcto y la estructura final del resultado.

**Casos de prueba:**

| # | Árbol (entrada)           | Salida esperada       | Resultado |
| - | ------------------------- | --------------------- | --------- |
| 1 | `[3,9,20,null,null,15,7]` | `[[3],[9,20],[15,7]]` | ✅         |
| 2 | `[1]`                     | `[[1]]`               | ✅         |
| 3 | `[]`                      | `[]`                  | ✅         |
| 4 | `[1,2,3,4,null,null,5]`   | `[[1],[2,3],[4,5]]`   | ✅         |
| 5 | `[1,2,null,3,null,4]`     | `[[1],[2],[3],[4]]`   | ✅         |

**🧠 Simulación paso a paso (ejemplo 1):**

```
queue = [3]
Nivel 0 → [3] → encolo [9,20]
Nivel 1 → [9,20] → encolo [15,7]
Nivel 2 → [15,7]
Resultado final = [[3],[9,20],[15,7]]
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                 |
| ---------------------- | ------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — cada nodo se procesa una vez.  |
| 💾 **Espacio:**        | O(W) — máximo ancho del árbol.        |
| ⚡ **Escalabilidad:**   | Excelente hasta árboles de 10⁴ nodos. |
| 🧩 **Tipo de patrón:** | BFS (cola + control de nivel).        |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Generalizar el patrón para otras variantes.

**🔧 Posibles optimizaciones:**

* Reutilizar el patrón para *Zigzag Level Order*, *Right Side View*, o *Average of Levels*.
* Sustituir `queue.shift()` por índices o `Deque` para mayor eficiencia en lenguajes donde el shift es costoso.
* DFS alternativo para reducir overhead de la cola si el árbol es pequeño.

**📚 Lecciones aprendidas:**

* BFS es ideal para *procesar árboles por niveles o distancias*.
* La clave está en usar el tamaño de la cola para delimitar cada nivel.
* Muchos problemas complejos se reducen a este patrón base con pequeñas modificaciones.

**✅ Conclusión final:**

> “Recorrí el árbol en anchura usando una cola.
> En cada nivel agrupé los valores y los agregué al resultado.
> La solución es lineal y fácilmente adaptable a otras variaciones de recorrido.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | BFS (Breadth-First Search)             |
| Complejidad      | O(N) tiempo, O(W) espacio              |
| Palabra clave    | “Recorrido por niveles”                |
| Tipo de problema | Árbol Binario / Recorrido estructurado |
| Nivel            | 🟡 Medio                               |

---