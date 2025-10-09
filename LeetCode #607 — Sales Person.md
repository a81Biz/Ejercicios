# 🧾 **LeetCode #607 — Sales Person**

> **Tema:** Subconsultas + exclusión condicional (`NOT IN`)
> **Nivel:** 🟢 Fácil
> **Patrón:** *Filtrado por exclusión cruzada entre tablas*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Usar subconsultas para filtrar filas basadas en relaciones con otras tablas.

**📖 Enunciado resumido:**
Dispones de tres tablas:

### 🧩 Tabla 1: `SalesPerson`

| Campo             | Tipo    | Descripción                |
| ----------------- | ------- | -------------------------- |
| `sales_id`        | int     | Identificador del vendedor |
| `name`            | varchar | Nombre del vendedor        |
| `salary`          | int     | Salario                    |
| `commission_rate` | int     | Porcentaje de comisión     |
| `hire_date`       | date    | Fecha de contratación      |

---

### 🧩 Tabla 2: `Company`

| Campo    | Tipo    | Descripción                 |
| -------- | ------- | --------------------------- |
| `com_id` | int     | Identificador de la empresa |
| `name`   | varchar | Nombre de la empresa        |
| `city`   | varchar | Ciudad donde opera          |

---

### 🧩 Tabla 3: `Orders`

| Campo        | Tipo | Descripción                        |
| ------------ | ---- | ---------------------------------- |
| `order_id`   | int  | ID del pedido                      |
| `order_date` | date | Fecha del pedido                   |
| `com_id`     | int  | Empresa compradora (clave foránea) |
| `sales_id`   | int  | Vendedor responsable               |
| `amount`     | int  | Monto del pedido                   |

---

**Tarea:**
Devuelve el **nombre de los vendedores que *no* trabajaron con la empresa `"RED"`**.

---

**💬 Reexplicación en voz alta:**

> “Debo listar los vendedores que **no tienen ningún pedido asociado a la empresa ‘RED’**.
> Si algún pedido de ese vendedor fue para RED, queda excluido.”

---

**❓ Preguntas al entrevistador:**

* ¿Debo mostrar solo el nombre? → ✅ Sí, únicamente `name`.
* ¿Qué pasa si un vendedor no tiene ningún pedido? → ✅ También debe aparecer (no trabajó con RED).
* ¿La comparación por nombre de empresa es sensible a mayúsculas? → ❌ No suele serlo en SQL estándar.
* ¿Puedo usar `JOIN` en lugar de subconsulta? → ✅ Sí, pero se busca practicar `NOT IN`.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Encontrar la lógica de exclusión entre tablas mediante subconsulta.

**📚 Tipo de problema:**
➡️ Filtrado con **subconsulta de exclusión (`NOT IN`)**.

**Estrategia:**

1. Identificar los vendedores que **sí trabajaron** con RED.
2. Excluirlos de la lista total de `SalesPerson`.
3. Mostrar solo el `name` de los que no aparecen en la subconsulta.

---

### 💡 Subconsulta (vendedores con RED)

```sql
SELECT o.sales_id
FROM Orders o
JOIN Company c ON o.com_id = c.com_id
WHERE c.name = 'RED';
```

### 💡 Consulta principal (exclusión)

```sql
SELECT name
FROM SalesPerson
WHERE sales_id NOT IN (
    SELECT o.sales_id
    FROM Orders o
    JOIN Company c ON o.com_id = c.com_id
    WHERE c.name = 'RED'
);
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Mostrar claridad en la estructura y buena legibilidad del SQL.

**✍️ Consulta SQL Final:**

```sql
SELECT
    name
FROM
    SalesPerson
WHERE
    sales_id NOT IN (
        SELECT o.sales_id
        FROM Orders o
        JOIN Company c
        ON o.com_id = c.com_id
        WHERE c.name = 'RED'
    );
```

**🗣️ Explicación hablada:**

> “Primero encuentro los `sales_id` que realizaron pedidos para la empresa RED.
> Luego selecciono todos los vendedores cuyo `sales_id` **no está** en esa lista.
> Así obtengo solo quienes nunca trabajaron con RED.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Comprobar que la subconsulta filtra correctamente los vendedores relacionados con RED.

---

### **Caso de prueba 1 — Mixto**

**SalesPerson**

| sales_id | name |
| -------- | ---- |
| 1        | John |
| 2        | Amy  |
| 3        | Mark |
| 4        | Pam  |

**Company**

| com_id | name   |
| ------ | ------ |
| 1      | RED    |
| 2      | ORANGE |

**Orders**

| order_id | com_id | sales_id |
| -------- | ------ | -------- |
| 1        | 1      | 1        |
| 2        | 2      | 3        |

**Salida esperada**

| name |
| ---- |
| Amy  |
| Pam  |
| Mark |

✅ Correcto — `John` fue excluido porque trabajó con `RED`.

---

### **Caso de prueba 2 — Ninguno trabajó con RED**

**Orders** vacía o sin `RED` → Todos los vendedores aparecen.

✅ Correcto.

---

### **Caso de prueba 3 — Todos trabajaron con RED**

**Salida esperada:** tabla vacía.

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                              |
| ---------------------- | -------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N + M) — una subconsulta y un `JOIN` simple.     |
| 💾 **Espacio:**        | O(1) — sin almacenamiento adicional.               |
| ⚡ **Escalabilidad:**   | Buena; depende del tamaño de `Orders` y `Company`. |
| 🧩 **Tipo de patrón:** | Subconsulta de exclusión (`NOT IN`).               |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Comprender variantes del patrón de exclusión y cómo mejorarlo.

**🔧 Posibles optimizaciones:**

* Cambiar `NOT IN` por `NOT EXISTS` para evitar problemas con valores `NULL`.
* Añadir índices sobre `Orders.sales_id` y `Company.name`.
* Reescribir con `LEFT JOIN` + `WHERE ... IS NULL` para bases grandes.

**Versión optimizada (usando `NOT EXISTS`):**

```sql
SELECT s.name
FROM SalesPerson s
WHERE NOT EXISTS (
    SELECT 1
    FROM Orders o
    JOIN Company c
    ON o.com_id = c.com_id
    WHERE o.sales_id = s.sales_id
    AND c.name = 'RED'
);
```

---

**📚 Lecciones aprendidas:**

* `NOT IN` excluye elementos que están en el resultado de una subconsulta.
* Si la subconsulta puede devolver `NULL`, es más seguro usar `NOT EXISTS`.
* Este patrón es clave para preguntas de tipo “mostrar los que no...”.

**✅ Conclusión final:**

> “Usé una subconsulta con `NOT IN` para excluir los vendedores
> que realizaron pedidos para la empresa RED.
> Es un ejemplo clásico de filtro de exclusión relacional.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | Subconsulta de exclusión (`NOT IN`)    |
| Complejidad      | O(N + M) tiempo                        |
| Palabra clave    | “Mostrar los que no trabajaron con...” |
| Tipo de problema | Filtro cruzado entre tablas            |
| Nivel            | 🟢 Fácil                               |

---