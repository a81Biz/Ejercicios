# 🧩 **LeetCode #70 — Climbing Stairs**

> **Tema:** Recursión + Dynamic Programming
> **Nivel:** 🟢 Fácil
> **Patrón:** *Recurrence Relation*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender qué se busca optimizar antes de escribir código.

**📖 Enunciado resumido:**
Estás subiendo una escalera con `n` escalones.
Cada vez puedes subir **1 o 2 escalones**.
Tu tarea es determinar **cuántas formas distintas** hay de llegar a la cima.

**🔢 Ejemplo de entrada:**
`n = 3`

**🎯 Ejemplo de salida esperada:**
`3` → [1+1+1], [1+2], [2+1]

**💬 Reexplicación (en voz alta):**

> “El número de maneras de subir `n` escalones depende de las combinaciones posibles de pasos de 1 y 2.
> Esto es un problema clásico de recurrencia, similar a Fibonacci.”

**❓ Preguntas al entrevistador:**

* ¿`n` siempre es positivo? → ✅ Sí, `n ≥ 1`.
* ¿Existe límite máximo? → Generalmente `n ≤ 45` en LeetCode.
* ¿Debo devolver el número exacto o solo el recuento? → Solo el **recuento**.
* ¿Qué pasa con `n = 0`? → Hay 1 forma (no moverse).

**🧩 Casos límite identificados:**

* [x] `n = 1` → 1
* [x] `n = 2` → 2
* [x] `n = 0` → 1
* [x] `n = 5` → 8 (verificación Fibonacci)

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Mostrar razonamiento estructurado y comprensión de patrones de recurrencia.

**📚 Tipo de problema detectado:**
➡️ **Recursión con subproblemas solapados (Dynamic Programming)**.

---

### 💡 Enfoque 1 — Recursión pura

**Idea:**
Cada vez que estás en el escalón `n`, puedes haber venido del escalón `n-1` o `n-2`.
Por tanto:

```
f(n) = f(n-1) + f(n-2)
```

**Base:**

```
f(1) = 1, f(2) = 2
```

**Complejidad:**
⏱️ Tiempo: `O(2^n)` (explota combinaciones)
💾 Espacio: `O(n)` (profundidad de recursión)

---

### ⚙️ Enfoque 2 — Recursión con Memoización (Top-Down)

**Idea:**
Evitar cálculos repetidos guardando resultados previos en un diccionario (memo).

**Complejidad:**
⏱️ Tiempo: `O(n)`
💾 Espacio: `O(n)`

---

### ⚡ Enfoque 3 — Programación Dinámica Iterativa (Bottom-Up)

**Idea:**
Usar una tabla o variables acumulativas:

```
f(1)=1, f(2)=2
f(3)=f(1)+f(2)=3
f(4)=f(2)+f(3)=5 ...
```

**Complejidad:**
⏱️ Tiempo: `O(n)`
💾 Espacio: `O(1)` — si usamos solo dos variables.

---

**🗺️ Plan de pasos (pseudocódigo):**

```
1. Si n <= 2 → retornar n.
2. Inicializar prev1 = 1, prev2 = 2.
3. Iterar desde 3 hasta n:
      actual = prev1 + prev2
      prev1 = prev2
      prev2 = actual
4. Retornar prev2.
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar con claridad y buenas prácticas.

**✍️ Implementación (JavaScript):**

```js
var climbStairs = function(n) {
    if (n <= 2) return n;

    let prev1 = 1;  // f(1)
    let prev2 = 2;  // f(2)
    let current = 0;

    for (let i = 3; i <= n; i++) {
        current = prev1 + prev2;
        prev1 = prev2;
        prev2 = current;
    }

    return prev2;
};
```

**🗣️ Explicación hablada:**

> “Inicio con los dos primeros casos base.
> Desde el tercer escalón en adelante, cada resultado depende de la suma de los dos anteriores.
> Actualizo los valores progresivamente hasta llegar a `n`.
> Al final retorno `prev2`, que contiene el número total de formas.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Confirmar que la solución funciona en todos los casos.

**🔢 Casos de prueba:**

| # | Entrada | Salida esperada | Resultado |
| - | ------- | --------------- | --------- |
| 1 | `n = 1` | `1`             | ✅         |
| 2 | `n = 2` | `2`             | ✅         |
| 3 | `n = 3` | `3`             | ✅         |
| 4 | `n = 4` | `5`             | ✅         |
| 5 | `n = 5` | `8`             | ✅         |
| 6 | `n = 0` | `1`             | ✅         |

**🧠 Simulación paso a paso:**

```
n=5
prev1=1, prev2=2
i=3 → current=3 → prev1=2, prev2=3
i=4 → current=5 → prev1=3, prev2=5
i=5 → current=8 → prev1=5, prev2=8
retorna 8
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                |
| ---------------------- | ------------------------------------ |
| ⏱️ **Tiempo:**         | O(N)                                 |
| 💾 **Espacio:**        | O(1)                                 |
| ⚡ **Escalabilidad:**   | Perfecta hasta n≈45–50 sin overflow. |
| 🧩 **Tipo de patrón:** | Dynamic Programming (Bottom-Up)      |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar pensamiento crítico y extensiones posibles.

**🔧 Posibles optimizaciones:**

* Implementar con memoización para comparar rendimientos.
* Usar BigInt si se requiere soporte para `n > 100`.
* Ampliar a casos donde se pueden subir 1, 2 o 3 escalones.

**📚 Lecciones aprendidas:**

* Muchos problemas recursivos pueden resolverse de forma iterativa optimizando memoria.
* La clave está en **detectar la relación de recurrencia**.

**✅ Conclusión final:**

> “Identifiqué la relación `f(n)=f(n-1)+f(n-2)` y la convertí en un algoritmo O(N) y O(1) de espacio.
> La solución es clara, eficiente y fácilmente explicable en una pizarra.”

---

📘 **Resumen final**

| Aspecto                       | Valor                                    |
| ----------------------------- | ---------------------------------------- |
| Patrón                        | Recursión / DP                           |
| Complejidad                   | O(N) tiempo, O(1) espacio                |
| Palabra clave                 | “Fibonacci iterativo”                    |
| Pregunta típica de entrevista | “¿Puedes hacerlo sin memoria adicional?” |
| Nivel                         | 🟢 Fácil                                 |

