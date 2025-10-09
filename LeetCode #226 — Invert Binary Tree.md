# 🌲 **LeetCode #226 — Invert Binary Tree**

> **Tema:** Árboles Binarios / DFS Recursivo
> **Nivel:** 🟢 Fácil
> **Patrón:** *Postorder Traversal (Swap de hijos)*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender el propósito del problema y las transformaciones requeridas.

**📖 Enunciado resumido:**
Dado el nodo raíz de un árbol binario, **invierte** el árbol y devuelve su raíz.
"Invertir" significa **intercambiar recursivamente los hijos izquierdo y derecho** de cada nodo.

**🔢 Ejemplo de entrada (visual):**

```
    4
   / \
  2   7
 / \ / \
1  3 6  9
```

**🎯 Salida esperada:**

```
    4
   / \
  7   2
 / \ / \
9  6 3  1
```

**💬 Reexplicación en voz alta:**

> “Por cada nodo del árbol, debo intercambiar sus ramas izquierda y derecha.
> Esto se aplica de forma recursiva desde la raíz hasta las hojas.”

**❓ Preguntas al entrevistador:**

* ¿Qué debo devolver al final? → La raíz del árbol invertido.
* ¿El árbol puede estar vacío? → ✅ Sí, en cuyo caso se devuelve `null`.
* ¿Hay valores duplicados en los nodos? → Puede haber, pero no afecta el resultado.
* ¿Puedo modificar el árbol en su lugar? → ✅ Sí, no es necesario crear uno nuevo.

**🧩 Casos límite:**

* [x] Árbol vacío (`root = null`) → salida `null`.
* [x] Árbol con un solo nodo → mismo árbol.
* [x] Árbol balanceado y desbalanceado.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Determinar el método más claro y eficiente para recorrer e intercambiar.

**📚 Tipo de problema:**
➡️ Recorrido completo del árbol con operación local → **DFS (Depth-First Search)**.

---

### 💡 Enfoque 1 — DFS Recursivo (Postorden)

**Idea:**
Recorrer el árbol en profundidad (izquierda → derecha → nodo),
y en cada nodo **intercambiar los hijos izquierdo y derecho**.

**Pseudocódigo:**

```
func invert(node):
    si node == null → return null

    invert(node.left)
    invert(node.right)

    temp = node.left
    node.left = node.right
    node.right = temp

    return node
```

**Complejidad:**
⏱️ Tiempo: `O(N)` (visitamos cada nodo una vez).
💾 Espacio: `O(H)` (altura del árbol, pila de recursión).

---

### ⚙️ Enfoque 2 — BFS Iterativo (Cola)

**Idea:**
Usar una cola (FIFO) para recorrer el árbol por niveles.
En cada iteración, hacer el intercambio y encolar los hijos si existen.

**Pseudocódigo:**

```
queue = [root]
mientras queue no vacía:
    node = queue.pop(0)
    intercambiar node.left y node.right
    si node.left existe → queue.push(node.left)
    si node.right existe → queue.push(node.right)
```

**Complejidad:**
⏱️ Tiempo: `O(N)`
💾 Espacio: `O(W)` (W = número máximo de nodos en un nivel).

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Mostrar claridad y estructura de pensamiento en la implementación.

**✍️ Implementación (JavaScript - DFS):**

```js
var invertTree = function(root) {
    if (!root) return null; // caso base

    // Intercambiar recursivamente los subárboles
    const left = invertTree(root.left);
    const right = invertTree(root.right);

    root.left = right;
    root.right = left;

    return root;
};
```

**🗣️ Explicación hablada:**

> “Primero verifico si el nodo existe.
> Luego invierto los subárboles izquierdo y derecho recursivamente.
> Finalmente los intercambio.
> La recursión garantiza que todos los niveles se procesen desde abajo hacia arriba.”

---

### 💡 Alternativa (BFS Iterativo):

```js
var invertTree = function(root) {
    if (!root) return null;

    const queue = [root];
    while (queue.length > 0) {
        const node = queue.shift();
        [node.left, node.right] = [node.right, node.left];

        if (node.left) queue.push(node.left);
        if (node.right) queue.push(node.right);
    }

    return root;
};
```

> “Uso una cola para recorrer el árbol por niveles.
> En cada nodo, invierto los punteros de sus hijos y continúo con el siguiente nivel.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Verificar comportamiento en distintas estructuras de árbol.

**Casos de prueba:**

| # | Árbol (entrada)   | Árbol invertido (salida esperada) | Resultado |
| - | ----------------- | --------------------------------- | --------- |
| 1 | `[4,2,7,1,3,6,9]` | `[4,7,2,9,6,3,1]`                 | ✅         |
| 2 | `[]`              | `[]`                              | ✅         |
| 3 | `[1]`             | `[1]`                             | ✅         |
| 4 | `[2,1,3]`         | `[2,3,1]`                         | ✅         |
| 5 | `[5,3,null,2,4]`  | `[5,null,3,null,4,2]`             | ✅         |

**🧠 Simulación (DFS):**

```
Nodo 4:
   invertir(2)
       invertir(1) → null
       invertir(3) → null
       intercambiar(1,3)
   invertir(7)
       invertir(6) → null
       invertir(9) → null
       intercambiar(6,9)
intercambiar(2,7)
return raíz invertida (4)
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                       |
| ---------------------- | ------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — cada nodo visitado una vez.          |
| 💾 **Espacio:**        | O(H) — altura del árbol (recursión).        |
| ⚡ **Escalabilidad:**   | Excelente para árboles de hasta ~10⁴ nodos. |
| 🧩 **Tipo de patrón:** | DFS postorden o BFS nivelado.               |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar pensamiento crítico y extensiones posibles.

**🔧 Posibles optimizaciones:**

* Si el árbol es muy profundo, preferir BFS para evitar desbordamiento de pila.
* Implementar versión in-place iterativa (ya lo hace BFS).
* Añadir validaciones para evitar llamadas innecesarias si los nodos son nulos.

**📚 Lecciones aprendidas:**

* El intercambio simétrico de nodos es una operación clásica de árboles binarios.
* DFS y BFS son dos caras del mismo patrón: recorrer y procesar.
* La claridad recursiva es clave: “resolver subproblemas y combinar resultados”.

**✅ Conclusión final:**

> “Recorrí el árbol recursivamente, intercambiando los hijos de cada nodo.
> La solución es clara, de tiempo lineal, y se explica fácilmente en pizarra.”

---

📘 **Resumen final**

| Aspecto          | Valor                                      |
| ---------------- | ------------------------------------------ |
| Patrón           | DFS Recursivo / BFS Iterativo              |
| Complejidad      | O(N) tiempo, O(H) espacio                  |
| Palabra clave    | “Swap hijos en postorden”                  |
| Tipo de problema | Árbol Binario / Transformación estructural |
| Nivel            | 🟢 Fácil                                   |

---
