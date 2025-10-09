# 🔗 **LeetCode #21 — Merge Two Sorted Lists**

> **Tema:** Listas Enlazadas + Recursión / Iteración
> **Nivel:** 🟢 Fácil
> **Patrón:** *Two Pointers / Recursión con retorno acumulativo*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender la estructura del problema y su objetivo antes de escribir código.

**📖 Enunciado resumido:**
Se te dan las cabeceras de **dos listas enlazadas ordenadas en orden ascendente**: `list1` y `list2`.
Tu tarea es **fusionarlas en una sola lista ordenada**, devolviendo el **nodo cabeza** de la lista combinada.

**🔢 Ejemplo de entrada:**

```
list1 = [1, 2, 4]
list2 = [1, 3, 4]
```

**🎯 Salida esperada:**

```
[1, 1, 2, 3, 4, 4]
```

**💬 Reexplicación en voz alta:**

> “Debo recorrer ambas listas y combinar sus nodos en orden ascendente, sin crear nuevos nodos si no es necesario.”

**❓ Preguntas al entrevistador:**

* ¿Puedo modificar las listas originales? → ✅ Sí, se puede reusar sus nodos.
* ¿Qué pasa si una lista está vacía? → Se devuelve la otra lista.
* ¿Los valores pueden repetirse? → ✅ Sí.
* ¿Se garantiza que ambas listas están ordenadas? → ✅ Sí.

**🧩 Casos límite:**

* [x] `list1 = []`, `list2 = []` → `[]`
* [x] `list1 = [1, 2, 3]`, `list2 = []` → `[1, 2, 3]`
* [x] `list1 = []`, `list2 = [0]` → `[0]`
* [x] Valores duplicados entre listas.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Mostrar cómo se puede resolver estructuradamente.

**📚 Tipo de problema:**
➡️ Fusión de estructuras lineales ordenadas → patrón clásico de **Two Pointers**.

---

### 💡 Enfoque 1 — Recursivo

**Idea:**
Comparar los valores de los nodos cabeza de ambas listas:

* Tomar el menor como cabeza de la lista fusionada.
* Avanzar en la lista de donde se tomó el nodo.
* Llamar recursivamente hasta que una de las listas quede vacía.

**Pseudocódigo:**

```
func merge(l1, l2):
    si l1 == null → return l2
    si l2 == null → return l1

    si l1.val < l2.val:
        l1.next = merge(l1.next, l2)
        return l1
    else:
        l2.next = merge(l1, l2.next)
        return l2
```

**Complejidad:**
⏱️ Tiempo: `O(m + n)`
💾 Espacio: `O(m + n)` (por la pila de recursión)

---

### ⚙️ Enfoque 2 — Iterativo con punteros

**Idea:**
Usar un **puntero ficticio (dummy)** para construir la lista.
Comparar `list1.val` y `list2.val`, y conectar el menor al `tail` de la lista fusionada.
Mover el puntero correspondiente hasta que una lista se acabe.

**Pseudocódigo:**

```
dummy = new Node(-1)
tail = dummy

while list1 && list2:
    if list1.val < list2.val:
        tail.next = list1
        list1 = list1.next
    else:
        tail.next = list2
        list2 = list2.next
    tail = tail.next

tail.next = list1 or list2
return dummy.next
```

**Complejidad:**
⏱️ Tiempo: `O(m + n)`
💾 Espacio: `O(1)`

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar con claridad y explicar las decisiones.

**✍️ Implementación (JavaScript):**

```js
var mergeTwoLists = function(list1, list2) {
    if (!list1) return list2;
    if (!list2) return list1;

    if (list1.val < list2.val) {
        list1.next = mergeTwoLists(list1.next, list2);
        return list1;
    } else {
        list2.next = mergeTwoLists(list1, list2.next);
        return list2;
    }
};
```

**🗣️ Explicación hablada:**

> “Comparo los valores de las cabeceras.
> El menor se convierte en la cabeza de la lista fusionada.
> Llamo recursivamente al resto de la lista correspondiente.
> Cada llamada conecta los nodos ordenadamente hasta que alguna lista se vacía.”

---

### 💡 Alternativa (Iterativa):

```js
var mergeTwoLists = function(list1, list2) {
    const dummy = new ListNode(-1);
    let tail = dummy;

    while (list1 && list2) {
        if (list1.val < list2.val) {
            tail.next = list1;
            list1 = list1.next;
        } else {
            tail.next = list2;
            list2 = list2.next;
        }
        tail = tail.next;
    }

    tail.next = list1 || list2;
    return dummy.next;
};
```

> “Uso un nodo ficticio `dummy` como ancla para no perder la referencia de la cabeza.
> Al final, conecto cualquier lista restante al resultado.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Confirmar correcto orden y manejo de casos límite.

**Casos de prueba:**

| # | Entrada              | Salida esperada | Resultado |
| - | -------------------- | --------------- | --------- |
| 1 | `[1,2,4]`, `[1,3,4]` | `[1,1,2,3,4,4]` | ✅         |
| 2 | `[]`, `[0]`          | `[0]`           | ✅         |
| 3 | `[2]`, `[1]`         | `[1,2]`         | ✅         |
| 4 | `[5,6]`, `[1,2,3,4]` | `[1,2,3,4,5,6]` | ✅         |
| 5 | `[1,1,1]`, `[1,1,1]` | `[1,1,1,1,1,1]` | ✅         |

**🧠 Simulación (recursiva):**

```
l1 = 1 → l2 = 1 → l2 no mayor → devolver l2 (1)
 → l2.next = merge(l1(1), l2.next(3))
 → comparar 1 < 3 → l1.next = merge(l1(2), l2(3))
 → ... → resultado final [1,1,2,3,4,4]
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                     | Valor                                      |
| --------------------------- | ------------------------------------------ |
| ⏱️ **Tiempo:**              | O(m + n)                                   |
| 💾 **Espacio (recursivo):** | O(m + n)                                   |
| 💾 **Espacio (iterativo):** | O(1)                                       |
| 🧩 **Tipo de patrón:**      | Two Pointers / Recursión                   |
| ⚡ **Escalabilidad:**        | Eficiente hasta decenas de miles de nodos. |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Analizar alternativas y robustez.

**🔧 Posibles optimizaciones:**

* Usar el enfoque iterativo para evitar desbordamiento de pila en lenguajes sin optimización de recursión.
* Agregar validaciones nulas antes de cada comparación.
* Reutilizar los nodos existentes en lugar de crear nuevos (ya implementado).

**📚 Lecciones aprendidas:**

* Este patrón es la base de *Merge Sort* y otras fusiones de estructuras ordenadas.
* Entender la recursión en estructuras enlazadas ayuda a razonar sobre problemas de árboles y grafos.
* Usar un nodo ficticio (`dummy`) es una técnica elegante para evitar condiciones especiales.

**✅ Conclusión final:**

> “Fusioné dos listas enlazadas ordenadas recorriéndolas con punteros.
> La solución es lineal en tiempo, constante en memoria (iterativa), y reutiliza los nodos existentes.”

---

📘 **Resumen final**

| Aspecto          | Valor                                   |
| ---------------- | --------------------------------------- |
| Patrón           | Two Pointers / Recursión                |
| Complejidad      | O(m+n) tiempo, O(1) espacio (iterativo) |
| Palabra clave    | “Dummy node”                            |
| Tipo de problema | Fusión de estructuras ordenadas         |
| Nivel            | 🟢 Fácil                                |

---