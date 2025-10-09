# 🌳 **LeetCode #98 — Validate Binary Search Tree**

> **Tema:** Árboles Binarios / Validación de propiedades
> **Nivel:** 🟡 Medio
> **Patrón:** *DFS con límites (min / max range check)*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender qué define un BST válido y cómo verificarlo recursivamente.

**📖 Enunciado resumido:**
Dado el nodo raíz de un árbol binario, determina si es un **árbol de búsqueda binario (BST)** válido.
Un árbol es BST si y solo si cumple estas condiciones:

1. El subárbol izquierdo contiene **solo valores menores** que el nodo actual.
2. El subárbol derecho contiene **solo valores mayores**.
3. Ambos subárboles también deben ser BST válidos.

**🔢 Ejemplo de entrada:**

```
    2
   / \
  1   3
```

**🎯 Salida esperada:**
`true`

**🔢 Ejemplo 2:**

```
    5
   / \
  1   4
     / \
    3   6
```

**🎯 Salida esperada:**
`false` (porque 3 está en el subárbol derecho de 5)

**💬 Reexplicación en voz alta:**

> “Debo verificar que cada nodo cumpla la regla de los rangos:
> todos los valores del lado izquierdo deben ser menores al nodo actual,
> y todos los del lado derecho deben ser mayores.”

**❓ Preguntas al entrevistador:**

* ¿Los valores pueden repetirse? → ❌ No, todos los valores deben ser únicos.
* ¿Puedo usar un recorrido in-order para validar el orden? → ✅ Sí, es una alternativa válida.
* ¿Qué pasa si el árbol está vacío? → Se considera BST válido (`true`).

**🧩 Casos límite:**

* [x] `root = null` → `true`
* [x] `root = [2,1,3]` → `true`
* [x] `root = [5,1,4,null,null,3,6]` → `false`
* [x] `root = [1,1]` → `false` (duplicado)

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Razonar sobre las formas de validar la propiedad BST en cada nodo.

**📚 Tipo de problema:**
➡️ Validación estructural recursiva — *DFS con propagación de límites válidos.*

---

### 💡 Enfoque 1 — DFS con límites (min / max)

**Idea:**

* Cada nodo tiene un rango válido `(min, max)` que proviene de su posición en el árbol.
* El nodo debe cumplir:

  ```
  min < node.val < max
  ```
* Luego, el subárbol izquierdo hereda el límite superior `node.val`,
  y el subárbol derecho hereda el límite inferior `node.val`.

**Pseudocódigo:**

```
func validate(node, min, max):
    si node == null → true
    si node.val <= min o node.val >= max → false
    return validate(node.left, min, node.val)
        && validate(node.right, node.val, max)
```

**Complejidad:**
⏱️ Tiempo: `O(N)` — se visita cada nodo una vez.
💾 Espacio: `O(H)` — altura del árbol (por recursión).

---

### ⚙️ Enfoque 2 — Inorder Traversal (orden ascendente)

**Idea:**

* Si haces un recorrido *inorder* (izquierda → raíz → derecha)
  y obtienes una lista de valores **estrictamente crecientes**,
  entonces el árbol es BST válido.

**Pseudocódigo:**

```
inorder = []
func traverse(node):
    si node == null → return
    traverse(node.left)
    si inorder no vacío y node.val <= último(inorder) → false
    inorder.push(node.val)
    traverse(node.right)
```

**Complejidad:**
⏱️ Tiempo: `O(N)`
💾 Espacio: `O(N)` (lista de valores visitados).

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Mostrar claridad en la validación y en el paso de límites.

**✍️ Implementación (JavaScript - DFS con límites):**

```js
var isValidBST = function(root) {
    const validate = (node, min, max) => {
        if (!node) return true;

        if (node.val <= min || node.val >= max) return false;

        return validate(node.left, min, node.val) &&
               validate(node.right, node.val, max);
    };

    return validate(root, -Infinity, Infinity);
};
```

**🗣️ Explicación hablada:**

> “Cada nodo debe estar dentro de un rango válido definido por sus ancestros.
> El límite inferior viene del valor de un ancestro derecho,
> y el superior de un ancestro izquierdo.
> Propago esos límites recursivamente hacia abajo.”

---

### 💡 Alternativa (Inorder Traversal):

```js
var isValidBST = function(root) {
    let prev = -Infinity;

    const inorder = (node) => {
        if (!node) return true;

        if (!inorder(node.left)) return false;
        if (node.val <= prev) return false;
        prev = node.val;
        return inorder(node.right);
    };

    return inorder(root);
};
```

> “Un BST produce una secuencia *inorder* estrictamente creciente.
> Si algún valor viola esa regla, devuelvo falso inmediatamente.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Probar árboles correctos e incorrectos con distintas configuraciones.

**Casos de prueba:**

| # | Árbol (entrada)            | Resultado esperado | Resultado |
| - | -------------------------- | ------------------ | --------- |
| 1 | `[2,1,3]`                  | `true`             | ✅         |
| 2 | `[5,1,4,null,null,3,6]`    | `false`            | ✅         |
| 3 | `[10,5,15,null,null,6,20]` | `false`            | ✅         |
| 4 | `[]`                       | `true`             | ✅         |
| 5 | `[1,1]`                    | `false`            | ✅         |

**🧠 Simulación (DFS con límites):**

```
Nodo 5: rango (-∞, ∞)
 → Nodo 1: rango (-∞, 5) ✅
 → Nodo 4: rango (5, ∞)
     → Nodo 3: rango (5, 4) ❌ (3 < 5)
Resultado: false
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                 |
| ---------------------- | ------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — visita cada nodo una vez.      |
| 💾 **Espacio:**        | O(H) — altura del árbol.              |
| ⚡ **Escalabilidad:**   | Perfecto hasta árboles de 10⁴ nodos.  |
| 🧩 **Tipo de patrón:** | DFS con propagación de restricciones. |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar capacidad de extender el patrón a problemas similares.

**🔧 Posibles optimizaciones:**

* Usar `inorder` iterativo con pila para evitar recursión profunda.
* Detener la validación tan pronto se detecta una violación (corte temprano).
* Reutilizar este patrón en problemas como:

  * *Lowest Common Ancestor*
  * *Recover BST*
  * *Serialize and Deserialize BST*

**📚 Lecciones aprendidas:**

* Un BST no es solo “izquierda < raíz < derecha”,
  sino que **toda la rama izquierda debe ser < raíz, y toda la derecha > raíz**.
* Pasar los límites correctamente (min y max) es la clave conceptual.
* El recorrido inorder garantiza orden creciente y permite validación alternativa.

**✅ Conclusión final:**

> “Validé el árbol propagando los límites permitidos para cada nodo.
> Si algún valor viola esos límites, el árbol deja de ser un BST.
> La solución es lineal, clara y fácilmente demostrable en una pizarra.”

---

📘 **Resumen final**

| Aspecto          | Valor                             |
| ---------------- | --------------------------------- |
| Patrón           | DFS con límites (min / max)       |
| Complejidad      | O(N) tiempo, O(H) espacio         |
| Palabra clave    | “Propagar rangos válidos”         |
| Tipo de problema | Validación estructural de árboles |
| Nivel            | 🟡 Medio                          |


