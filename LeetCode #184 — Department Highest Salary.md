# 🏢 **LeetCode #184 — Department Highest Salary**

> **Tema:** `JOIN`, `GROUP BY` y subconsultas con agregación
> **Nivel:** 🟡 Medio
> **Patrón:** *Agrupación con filtrado por máximo valor (Top 1 por grupo)*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Obtener el empleado con el salario más alto en cada departamento.

**📖 Enunciado resumido:**
Tienes dos tablas:

### 🧩 Tabla 1: `Employee`

| Campo          | Tipo    | Descripción                                    |
| -------------- | ------- | ---------------------------------------------- |
| `id`           | int     | Identificador del empleado                     |
| `name`         | varchar | Nombre del empleado                            |
| `salary`       | int     | Salario del empleado                           |
| `departmentId` | int     | Identificador del departamento (clave foránea) |

### 🧩 Tabla 2: `Department`

| Campo  | Tipo    | Descripción                    |
| ------ | ------- | ------------------------------ |
| `id`   | int     | Identificador del departamento |
| `name` | varchar | Nombre del departamento        |

---

**Tarea:**
Devuelve el **nombre del departamento, el nombre del empleado y su salario**,
pero solo para los **empleados con el salario más alto de cada departamento.**

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

> “Debo mostrar, por cada departamento, al o los empleados con el salario máximo.
> Puede haber empates (más de un empleado con el mismo máximo).”

---

**❓ Preguntas al entrevistador:**

* ¿Pueden existir empates de salario? → ✅ Sí, deben mostrarse todos.
* ¿Qué pasa si un departamento no tiene empleados? → ❌ No aparece.
* ¿Se requiere orden específico? → No, pero se puede ordenar por departamento.
* ¿La columna de salida debe tener nombres específicos? → ✅ Sí: `Department`, `Employee`, `Salary`.

**🧩 Casos límite:**

* [x] Dos empleados con mismo salario máximo → se muestran ambos.
* [x] Departamento sin empleados → no aparece.
* [x] Todos con mismo salario → se muestran todos.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Obtener el máximo salario por departamento y unirlo con los datos de empleados.

**📚 Tipo de problema:**
➡️ Agregación + Filtro + `JOIN`.

---

### 💡 Enfoque 1 — Subconsulta con `MAX(salary)`

**Idea:**

1. Crear una subconsulta que obtenga el salario máximo por cada departamento:

   ```sql
   SELECT departmentId, MAX(salary) AS max_salary
   FROM Employee
   GROUP BY departmentId
   ```
2. Unir esa subconsulta con la tabla `Employee` para filtrar solo los empleados con ese salario.
3. Unir finalmente con `Department` para mostrar el nombre del departamento.

---

**Pseudocódigo SQL:**

```sql
SELECT
    d.name AS Department,
    e.name AS Employee,
    e.salary AS Salary
FROM
    Employee e
JOIN
    Department d
    ON e.departmentId = d.id
JOIN
    (
        SELECT departmentId, MAX(salary) AS max_salary
        FROM Employee
        GROUP BY departmentId
    ) AS m
    ON e.departmentId = m.departmentId
    AND e.salary = m.max_salary;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara, alias consistentes y nombres requeridos en la salida.

**✍️ Consulta SQL Final:**

```sql
SELECT
    d.name AS Department,
    e.name AS Employee,
    e.salary AS Salary
FROM
    Employee e
JOIN
    Department d
    ON e.departmentId = d.id
JOIN
    (
        SELECT departmentId, MAX(salary) AS max_salary
        FROM Employee
        GROUP BY departmentId
    ) AS m
    ON e.departmentId = m.departmentId
    AND e.salary = m.max_salary;
```

**🗣️ Explicación hablada:**

> “Primero calculo el salario máximo por cada departamento.
> Luego uno esa subconsulta con la tabla `Employee` para quedarme solo con quienes tienen ese salario.
> Finalmente uno con `Department` para mostrar el nombre del área.
> Así obtengo el máximo salario por departamento y todos los empleados que lo comparten.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Verificar que los máximos por grupo se identifican correctamente.

### **Caso de prueba 1 — Empate**

**Employee**

| id | name  | salary | departmentId |
| -- | ----- | ------ | ------------ |
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Max   | 90000  | 1            |
| 4  | Sam   | 60000  | 2            |
| 5  | Henry | 80000  | 2            |

**Department**

| id | name  |
| -- | ----- |
| 1  | IT    |
| 2  | Sales |

**Salida esperada**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| IT         | Jim      | 90000  |
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |

✅ Correcto.

---

### **Caso de prueba 2 — Todos distintos**

**Employee**

| name  | salary | departmentId |
| ----- | ------ | ------------ |
| Alice | 50000  | 1            |
| Bob   | 40000  | 2            |
| Carol | 30000  | 3            |

**Department**

| id | name  |
| -- | ----- |
| 1  | HR    |
| 2  | IT    |
| 3  | Admin |

**Salida esperada**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| HR         | Alice    | 50000  |
| IT         | Bob      | 40000  |
| Admin      | Carol    | 30000  |

✅ Correcto.

---

### **Caso de prueba 3 — Departamento sin empleados**

No aparece en los resultados.
✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                                 |
| ---------------------- | ----------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N log N) — agrupación + uniones.                    |
| 💾 **Espacio:**        | O(D) — una fila por departamento en subconsulta.      |
| ⚡ **Escalabilidad:**   | Excelente; se optimiza con índices en `departmentId`. |
| 🧩 **Tipo de patrón:** | Agregación con subconsulta (Top 1 por grupo).         |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar comprensión del patrón “máximo por grupo”.

**🔧 Posibles optimizaciones:**

* En MySQL 8+ o PostgreSQL, usar **funciones ventana (`ROW_NUMBER`)**:

  ```sql
  SELECT Department, Employee, Salary
  FROM (
      SELECT
          d.name AS Department,
          e.name AS Employee,
          e.salary AS Salary,
          ROW_NUMBER() OVER (
              PARTITION BY e.departmentId ORDER BY e.salary DESC
          ) AS rn
      FROM Employee e
      JOIN Department d ON e.departmentId = d.id
  ) ranked
  WHERE rn = 1;
  ```
* Crear índices en `departmentId` para acelerar el `JOIN`.
* Reutilizar el patrón para obtener:

  * *Department Lowest Salary*
  * *Department Average Salary Above X*

**📚 Lecciones aprendidas:**

* El patrón `JOIN + GROUP BY` es esencial para problemas de agregación.
* Las subconsultas internas permiten comparar valores con los máximos o mínimos de su grupo.
* Entender el emparejamiento por claves evita errores lógicos en entrevistas.

**✅ Conclusión final:**

> “Calculé el salario máximo por departamento con una subconsulta,
> y luego uní con `Employee` y `Department` para mostrar solo los empleados que lo alcanzan.
> Este patrón demuestra dominio de agregaciones y relaciones.”

---

📘 **Resumen final**

| Aspecto          | Valor                          |
| ---------------- | ------------------------------ |
| Patrón           | `JOIN` + subconsulta con `MAX` |
| Complejidad      | O(N log N) tiempo              |
| Palabra clave    | “Máximo por grupo”             |
| Tipo de problema | Agregación relacional SQL      |
| Nivel            | 🟡 Medio                       |

