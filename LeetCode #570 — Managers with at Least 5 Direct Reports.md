# 🧑‍💼 **LeetCode #570 — Managers with at Least 5 Direct Reports**

> **Tema:** Subconsultas correlacionadas y relaciones jerárquicas
> **Nivel:** 🔴 Difícil
> **Patrón:** *Identificación de agregados jerárquicos dentro de una misma tabla*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Encontrar a los empleados que son **managers** con **al menos 5 subordinados directos**.

---

**📖 Enunciado resumido:**
La tabla `Employee` contiene los siguientes campos:

| Campo        | Tipo    | Descripción                                   |
| ------------ | ------- | --------------------------------------------- |
| `id`         | int     | Identificador del empleado                    |
| `name`       | varchar | Nombre del empleado                           |
| `department` | varchar | Departamento                                  |
| `managerId`  | int     | ID del manager (puede ser `NULL` si no tiene) |

---

**Tarea:**
Devuelve el nombre de todos los **managers** que tienen **cinco o más empleados directos**.

---

**🔢 Ejemplo de datos:**

| id | name  | department | managerId |
| -- | ----- | ---------- | --------- |
| 1  | John  | A          | null      |
| 2  | Dan   | A          | 1         |
| 3  | James | A          | 1         |
| 4  | Amy   | A          | 1         |
| 5  | Anne  | A          | 1         |
| 6  | Ron   | A          | 1         |

**🎯 Salida esperada:**

| name |
| ---- |
| John |

---

**💬 Reexplicación en voz alta:**

> “Necesito identificar a los empleados cuyo `id` aparece como `managerId`
> en al menos cinco filas distintas.”

---

**❓ Preguntas al entrevistador:**

* ¿Solo cuentan los subordinados directos (no jerárquicos)? → ✅ Sí, solo directos.
* ¿Qué pasa si el `managerId` es `NULL`? → ❌ No tiene manager.
* ¿Y si hay varios managers con el mismo número? → ✅ Todos los que cumplan ≥5.
* ¿Se debe mostrar algo más que el nombre? → ❌ Solo `name`.

**🧩 Casos límite:**

* [x] Un solo empleado sin manager → vacío.
* [x] Manager con exactamente 5 → incluido.
* [x] Manager con menos de 5 → excluido.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Contar cuántos empleados tienen cada `managerId` y filtrar por ≥ 5.

**📚 Tipo de problema:**
➡️ *Auto-relación (self-join) o subconsulta correlacionada.*

---

### 💡 Enfoque 1 — Subconsulta correlacionada (clásico)

1. Tomar cada empleado `e1`.
2. Contar cuántos empleados (`e2`) lo tienen como `managerId`.
3. Si la cuenta ≥ 5 → incluir su nombre.

---

**Pseudocódigo SQL:**

```sql
SELECT name
FROM Employee e1
WHERE (
    SELECT COUNT(*)
    FROM Employee e2
    WHERE e2.managerId = e1.id
) >= 5;
```

---

### 💡 Enfoque 2 — `GROUP BY` + `HAVING`

```sql
SELECT e1.name
FROM Employee e1
JOIN Employee e2
ON e1.id = e2.managerId
GROUP BY e1.id, e1.name
HAVING COUNT(e2.id) >= 5;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara, sin redundancias, compatible con MySQL o PostgreSQL.

**✍️ Consulta SQL Final (versión con `GROUP BY`):**

```sql
SELECT
    e1.name
FROM
    Employee e1
JOIN
    Employee e2
    ON e1.id = e2.managerId
GROUP BY
    e1.id, e1.name
HAVING
    COUNT(e2.id) >= 5;
```

**🗣️ Explicación hablada:**

> “Uno la tabla consigo misma (`self-join`) para vincular managers con sus empleados directos.
> Agrupo por el ID y nombre del manager y aplico un filtro `HAVING COUNT >= 5`
> para mostrar solo aquellos que tienen cinco o más subordinados.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Confirmar que solo se devuelven managers con ≥ 5 subordinados.

### **Caso de prueba 1 — Ejemplo base**

| id | name  | managerId |
| -- | ----- | --------- |
| 1  | John  | NULL      |
| 2  | Dan   | 1         |
| 3  | James | 1         |
| 4  | Amy   | 1         |
| 5  | Anne  | 1         |
| 6  | Ron   | 1         |

**Salida esperada**

| name |
| ---- |
| John |

✅ Correcto.

---

### **Caso de prueba 2 — Dos managers válidos**

| id | name | managerId |
| -- | ---- | --------- |
| 1  | A    | NULL      |
| 2  | B    | 1         |
| 3  | C    | 1         |
| 4  | D    | 1         |
| 5  | E    | 1         |
| 6  | F    | 1         |
| 7  | X    | NULL      |
| 8  | Y    | 7         |
| 9  | Z    | 7         |
| 10 | W    | 7         |
| 11 | T    | 7         |
| 12 | G    | 7         |

**Salida esperada**

| name |
| ---- |
| A    |
| X    |

✅ Correcto.

---

### **Caso de prueba 3 — Ninguno cumple**

Todos los managers tienen menos de 5 empleados.
**Salida esperada:** vacío ✅

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                                             |
| ---------------------- | ----------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N²) con subconsulta correlacionada / O(N log N) con `GROUP BY`. |
| 💾 **Espacio:**        | O(M) — una fila por manager.                                      |
| ⚡ **Escalabilidad:**   | Muy buena con índices en `managerId`.                             |
| 🧩 **Tipo de patrón:** | Jerarquía directa (auto-relación).                                |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar comprensión de relaciones jerárquicas y subconsultas dependientes.

**🔧 Posibles optimizaciones:**

* Agregar índice en `managerId` para acelerar el `JOIN`.
* Usar `EXPLAIN` para verificar plan de ejecución.
* Con CTE recursivo (`WITH RECURSIVE`), podrías extenderlo a managers de *segundo nivel*:

  ```sql
  WITH RECURSIVE hierarchy AS (
      SELECT id, name, managerId
      FROM Employee
      WHERE managerId IS NULL
      UNION ALL
      SELECT e.id, e.name, e.managerId
      FROM Employee e
      JOIN hierarchy h ON e.managerId = h.id
  )
  SELECT managerId, COUNT(*) AS total
  FROM hierarchy
  GROUP BY managerId;
  ```

**📚 Lecciones aprendidas:**

* Los *self-joins* permiten analizar jerarquías en una sola tabla.
* `HAVING` se usa para filtrar agregaciones después del `GROUP BY`.
* Entender relaciones *padre-hijo* es esencial en modelos organizacionales y gráficos.

**✅ Conclusión final:**

> “Identifiqué los managers con al menos 5 subordinados directos
> usando un `self-join` sobre `Employee` y agrupación con `HAVING COUNT >= 5`.
> Este patrón demuestra comprensión de jerarquías, subconsultas y optimización con índices.”

---

📘 **Resumen final**

| Aspecto          | Valor                               |
| ---------------- | ----------------------------------- |
| Patrón           | `self-join` + `GROUP BY` + `HAVING` |
| Complejidad      | O(N log N)                          |
| Palabra clave    | “Jerarquía directa de empleados”    |
| Tipo de problema | Relación recursiva SQL              |
| Nivel            | 🔴 Difícil                          |

