# 🧾 **LeetCode #1581 — Customer Who Visited but Did Not Make Any Transactions**

> **Tema:** `LEFT JOIN` + `IS NULL` + subconsultas de exclusión
> **Nivel:** 🔴 Difícil
> **Patrón:** *Detección de registros sin correspondencia entre tablas*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Encontrar los clientes que **visitaron** la tienda pero **no realizaron transacciones**,
> y contar cuántas veces visitaron sin comprar.

---

**📖 Enunciado resumido:**
Se tienen dos tablas:

### 🧩 Tabla 1: `Visits`

| Campo         | Tipo | Descripción                |
| ------------- | ---- | -------------------------- |
| `visit_id`    | int  | Identificador de la visita |
| `customer_id` | int  | ID del cliente             |

---

### 🧩 Tabla 2: `Transactions`

| Campo            | Tipo | Descripción                     |
| ---------------- | ---- | ------------------------------- |
| `transaction_id` | int  | Identificador de la transacción |
| `visit_id`       | int  | ID de la visita correspondiente |
| `amount`         | int  | Monto gastado                   |

---

**Tarea:**
Devuelve el `customer_id` y el número de visitas (`count_no_trans`)
en las que **no hubo ninguna transacción asociada**.

---

**🔢 Ejemplo de datos:**

**Visits**

| visit_id | customer_id |
| -------- | ----------- |
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |

**Transactions**

| transaction_id | visit_id | amount |
| -------------- | -------- | ------ |
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 7        | 200    |
| 12             | 1        | 910    |

**🎯 Salida esperada:**

| customer_id | count_no_trans |
| ----------- | -------------- |
| 9           | 1              |
| 30          | 1              |
| 54          | 1              |
| 96          | 1              |

---

**💬 Reexplicación en voz alta:**

> “Cada visita puede o no tener transacciones.
> Necesito agrupar las visitas por cliente y contar cuántas no aparecen en la tabla `Transactions`.”

---

**❓ Preguntas al entrevistador:**

* ¿Puede haber múltiples transacciones por visita? → ✅ Sí, pero cuenta como una sola.
* ¿Qué pasa si un cliente nunca visitó? → ❌ No se incluye.
* ¿Qué pasa si un cliente siempre compró? → ❌ No se incluye.
* ¿Debe ordenarse el resultado? → ✅ Por `customer_id`.

**🧩 Casos límite:**

* [x] Cliente con visitas mixtas (unas con compra, otras sin).
* [x] Clientes con visitas sin transacción.
* [x] Cliente con todas sus visitas con transacción → se omite.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Detectar las visitas que no tienen transacciones asociadas y contarlas por cliente.

**📚 Tipo de problema:**
➡️ Comparación de existencia (`LEFT JOIN` + `IS NULL`) o `NOT EXISTS`.

---

### 💡 Enfoque 1 — `LEFT JOIN` + `IS NULL`

1. Unir `Visits` con `Transactions` por `visit_id`.
2. Conservar todas las visitas (`LEFT JOIN`).
3. Filtrar las que **no tienen correspondencia** (`transaction_id IS NULL`).
4. Agrupar por `customer_id` y contar.

---

**Pseudocódigo SQL:**

```sql
SELECT
    v.customer_id,
    COUNT(v.visit_id) AS count_no_trans
FROM Visits v
LEFT JOIN Transactions t
ON v.visit_id = t.visit_id
WHERE t.transaction_id IS NULL
GROUP BY v.customer_id
ORDER BY v.customer_id;
```

---

### 💡 Enfoque 2 — `NOT EXISTS`

```sql
SELECT
    v.customer_id,
    COUNT(*) AS count_no_trans
FROM Visits v
WHERE NOT EXISTS (
    SELECT 1
    FROM Transactions t
    WHERE t.visit_id = v.visit_id
)
GROUP BY v.customer_id
ORDER BY v.customer_id;
```

*(Ambos enfoques producen el mismo resultado; `NOT EXISTS` suele ser más legible.)*

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara, con nombres consistentes y resultado ordenado.

**✍️ Consulta SQL Final (versión `LEFT JOIN`):**

```sql
SELECT
    v.customer_id,
    COUNT(v.visit_id) AS count_no_trans
FROM
    Visits v
LEFT JOIN
    Transactions t
    ON v.visit_id = t.visit_id
WHERE
    t.transaction_id IS NULL
GROUP BY
    v.customer_id
ORDER BY
    v.customer_id;
```

**🗣️ Explicación hablada:**

> “Hago una unión `LEFT JOIN` entre `Visits` y `Transactions`
> para conservar todas las visitas, incluso las que no tienen transacción.
> Luego filtro aquellas con `transaction_id IS NULL`,
> agrupo por cliente y cuento cuántas visitas sin transacción tiene cada uno.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Confirmar que solo se cuentan visitas sin transacción.

### **Caso de prueba 1 — Ejemplo base**

✅ Resultado:

| customer_id | count_no_trans |
| ----------- | -------------- |
| 9           | 1              |
| 30          | 1              |
| 54          | 1              |
| 96          | 1              |

Correcto.

---

### **Caso de prueba 2 — Todos compran**

**Transactions**

| visit_id | amount |
| -------- | ------ |
| 1        | 500    |
| 2        | 200    |
| 3        | 100    |

✅ Resultado: vacío.

---

### **Caso de prueba 3 — Todos sin compra**

**Transactions:** *(vacía)*
✅ Resultado: todos los `customer_id` con total de visitas.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                                 |
| ---------------------- | ----------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N log N) — con índice en `visit_id`.                |
| 💾 **Espacio:**        | O(C) — una fila por cliente.                          |
| ⚡ **Escalabilidad:**   | Excelente con índices (`visit_id`, `transaction_id`). |
| 🧩 **Tipo de patrón:** | Detección de faltantes (`LEFT JOIN` + `IS NULL`).     |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de relaciones de exclusión y detección de ausencias.

**🔧 Posibles optimizaciones:**

* Reemplazar `LEFT JOIN` por `NOT EXISTS` para grandes volúmenes de datos.
* Crear índices en `Transactions.visit_id` para acelerar búsqueda.
* Usar `DISTINCT` si hay riesgo de duplicidad en `Transactions`.

**📚 Lecciones aprendidas:**

* `LEFT JOIN` y `IS NULL` son la forma más clara de encontrar registros sin correspondencia.
* `NOT EXISTS` es equivalente y suele ser más eficiente en bases grandes.
* Este patrón se usa para detectar “faltantes” en reportes financieros, inventarios, logs y auditorías.

**✅ Conclusión final:**

> “Uní `Visits` con `Transactions` y filtré los casos donde no hay transacción (`IS NULL`).
> Agrupé por cliente para contar sus visitas sin compra.
> Este patrón es clave para detectar eventos esperados que no ocurrieron.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | `LEFT JOIN` + `IS NULL` / `NOT EXISTS` |
| Complejidad      | O(N log N)                             |
| Palabra clave    | “Ausencia de correspondencia”          |
| Tipo de problema | Detección de registros faltantes       |
| Nivel            | 🔴 Difícil                             |

