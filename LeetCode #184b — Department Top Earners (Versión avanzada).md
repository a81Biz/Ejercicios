# 🏛️ **LeetCode #184b — Department Top Earners (Versión avanzada)**

> **Tema:** Subconsultas correlacionadas y agregaciones dependientes
> **Nivel:** 🔴 Difícil
> **Patrón:** *Filtrado por máximo dentro de grupo usando subconsultas anidadas*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Obtener el nombre y salario de los empleados con el **salario más alto en su departamento**,
> pero usando **subconsultas correlacionadas** (no `JOIN` ni funciones ventana).

---

**📖 Enunciado resumido:**
Se tienen dos tablas:

### 🧩 Tabla 1: `Employee`

| Campo          | Tipo    | Descripción                |
| -------------- | ------- | -------------------------- |
| `id`           | int     | Identificador del empleado |
| `name`         | varchar | Nombre del empleado        |
| `salary`       | int     | Salario                    |
| `departmentId` | int     | ID del departamento        |

---

### 🧩 Tabla 2: `Department`

| Campo  | Tipo    | Descripción                    |
| ------ | ------- | ------------------------------ |
| `id`   | int     | Identificador del departamento |
| `name` | varchar | Nombre del departamento        |

---

**Tarea:**
Devuelve el **nombre del departamento, el nombre del empleado y su salario**,
solo para los empleados que tengan el **mayor salario dentro de su departamento**.

✅ Se permite empates (varios empleados con el mismo salario máximo).

---

**🔢 Ejemplo de datos:**

**Employee**

| id | name  | salary | departmentId |
| -- | ----- | ------ | ------------ |
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |

**Department**

| id | name  |
| -- | ----- |
| 1  | IT    |
| 2  | Sales |

**🎯 Salida esperada:**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| IT         | Jim      | 90000  |
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |

---

**💬 Reexplicación en voz alta:**

> “Por cada departamento, debo comparar cada empleado con los demás del mismo departamento
> y mostrar solo aquellos cuyo salario sea igual al salario máximo dentro de ese grupo.”

---

**❓ Preguntas al entrevistador:**

* ¿Puede haber empates? → ✅ Sí, deben mostrarse todos.
* ¿Qué pasa si un departamento no tiene empleados? → ❌ No aparece.
* ¿Se permite usar `JOIN`? → ❌ En esta versión se pide subconsulta correlacionada.
* ¿El resultado debe ordenarse? → ✅ Por departamento (opcional).

**🧩 Casos límite:**

* [x] Empates de salario máximo → múltiples resultados.
* [x] Departamentos con 1 empleado → aparece ese único registro.
* [x] Departamentos sin empleados → no aparecen.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Para cada empleado, verificar si su salario coincide con el salario máximo de su departamento.

**📚 Tipo de problema:**
➡️ Subconsulta correlacionada dependiente del campo `departmentId`.

---

### 💡 Enfoque 1 — Subconsulta correlacionada

1. Para cada fila en `Employee e`, ejecutar una subconsulta que calcule
   el `MAX(salary)` dentro del mismo `departmentId`.
2. Si el salario del empleado es igual a ese máximo → incluirlo.
3. Unir con `Department` solo al final para obtener el nombre del departamento.

---

**Pseudocódigo SQL:**

```sql
SELECT
    (SELECT name FROM Department WHERE id = e.departmentId) AS Department,
    e.name AS Employee,
    e.salary AS Salary
FROM
    Employee e
WHERE
    e.salary = (
        SELECT MAX(salary)
        FROM Employee
        WHERE departmentId = e.departmentId
    );
```

---

### 💡 Enfoque 2 — Equivalente con `JOIN` (solo si el entrevistador lo permite)

```sql
SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary
FROM Employee e
JOIN Department d ON e.departmentId = d.id
WHERE (e.departmentId, e.salary) IN (
    SELECT departmentId, MAX(salary)
    FROM Employee
    GROUP BY departmentId
);
```

*(La versión de arriba es más eficiente, pero la correlacionada demuestra comprensión más profunda.)*

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Subconsulta correlacionada clara, legible y con alias consistentes.

**✍️ Consulta SQL Final (versión correlacionada):**

```sql
SELECT
    (SELECT name FROM Department WHERE id = e.departmentId) AS Department,
    e.name AS Employee,
    e.salary AS Salary
FROM
    Employee e
WHERE
    e.salary = (
        SELECT MAX(salary)
        FROM Employee
        WHERE departmentId = e.departmentId
    )
ORDER BY Department;
```

**🗣️ Explicación hablada:**

> “Para cada empleado `e`, ejecuto una subconsulta que obtiene el salario máximo del departamento
> al que pertenece (`e.departmentId`).
> Si su salario coincide con ese máximo, se incluye en el resultado.
> Finalmente, muestro el nombre del departamento mediante una subconsulta simple.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Verificar que los máximos por departamento se obtienen correctamente incluso con empates.

---

### **Caso de prueba 1 — Ejemplo base**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| IT         | Jim      | 90000  |
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |

✅ Correcto.

---

### **Caso de prueba 2 — Departamento con 1 empleado**

| id | name  | salary | departmentId |
| -- | ----- | ------ | ------------ |
| 7  | Alice | 50000  | 3            |

| Department |
| ---------- |
| HR         |

**Salida esperada**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| HR         | Alice    | 50000  |

✅ Correcto.

---

### **Caso de prueba 3 — Empates múltiples**

| id | name | salary | departmentId |
| -- | ---- | ------ | ------------ |
| 1  | A    | 60000  | 1            |
| 2  | B    | 60000  | 1            |
| 3  | C    | 40000  | 1            |

**Salida esperada**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| IT         | A        | 60000  |
| IT         | B        | 60000  |

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                                               | Valor                                   |
| ----------------------------------------------------- | --------------------------------------- |
| ⏱️ **Tiempo:**                                        | O(N²) — subconsulta ejecutada por fila. |
| 💾 **Espacio:**                                       | O(1).                                   |
| ⚡ **Escalabilidad:**                                  | Buena para conjuntos pequeños;          |
| se optimiza con índices en `departmentId` y `salary`. |                                         |
| 🧩 **Tipo de patrón:**                                | Subconsulta correlacionada dependiente. |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar comprensión de comparaciones dependientes por grupo.

**🔧 Posibles optimizaciones:**

* Agregar índice `(departmentId, salary)` para reducir costo de subconsulta.
* Cambiar a `JOIN` + `IN` para bases grandes.
* En PostgreSQL o MySQL 8+, se puede usar `RANK()` para resolver en O(N log N):

  ```sql
  SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary
  FROM (
      SELECT name, salary, departmentId,
             RANK() OVER (PARTITION BY departmentId ORDER BY salary DESC) AS rnk
      FROM Employee
  ) e
  JOIN Department d ON e.departmentId = d.id
  WHERE rnk = 1;
  ```

**📚 Lecciones aprendidas:**

* Las subconsultas correlacionadas comparan valores en contexto (fila a fila).
* Este patrón se usa cuando la condición depende del grupo al que pertenece cada registro.
* Aprender a reescribir correlaciones como `JOIN` es clave para optimización avanzada.

**✅ Conclusión final:**

> “Comparé el salario de cada empleado con el máximo dentro de su departamento
> usando una subconsulta correlacionada.
> Este patrón demuestra dominio de agregaciones dependientes y contexto de ejecución en SQL.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | Subconsulta correlacionada dependiente |
| Complejidad      | O(N²)                                  |
| Palabra clave    | “Máximo por grupo dependiente”         |
| Tipo de problema | Comparación interna jerárquica         |
| Nivel            | 🔴 Difícil                             |

---