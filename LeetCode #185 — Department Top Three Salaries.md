# 🏆 **LeetCode #185 — Department Top Three Salaries**

> **Tema:** Funciones ventana (`DENSE_RANK`, `ROW_NUMBER`)
> **Nivel:** 🟡 Medio
> **Patrón:** *Ranking y filtrado por posición dentro de cada grupo*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Mostrar los empleados con los tres salarios más altos en cada departamento.

**📖 Enunciado resumido:**
Se tienen las mismas tablas que en el ejercicio anterior:

### 🧩 Tabla 1: `Employee`

| Campo          | Tipo    | Descripción                |
| -------------- | ------- | -------------------------- |
| `id`           | int     | Identificador del empleado |
| `name`         | varchar | Nombre del empleado        |
| `salary`       | int     | Salario                    |
| `departmentId` | int     | ID del departamento        |

### 🧩 Tabla 2: `Department`

| Campo  | Tipo    | Descripción                    |
| ------ | ------- | ------------------------------ |
| `id`   | int     | Identificador del departamento |
| `name` | varchar | Nombre del departamento        |

---

**Tarea:**
Devuelve el **nombre del departamento, el nombre del empleado y su salario**,
pero solo para los **tres salarios más altos** de cada departamento.

---

**🔢 Ejemplo de datos:**

**Employee**

| id | name  | salary | departmentId |
| -- | ----- | ------ | ------------ |
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |

**Department**

| id | name  |
| -- | ----- |
| 1  | IT    |
| 2  | Sales |

**🎯 Salida esperada:**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |

---

**💬 Reexplicación en voz alta:**

> “Por cada departamento, debo mostrar a los empleados con los tres salarios más altos,
> incluyendo empates si los hay. El ranking se reinicia para cada departamento.”

---

**❓ Preguntas al entrevistador:**

* ¿Qué pasa si un departamento tiene menos de tres empleados? → ✅ Se muestran todos.
* ¿Qué pasa con salarios repetidos? → ✅ Ambos deben mostrarse (usar `DENSE_RANK`).
* ¿Debe haber un orden específico? → No, pero se puede ordenar por departamento.
* ¿Hay límite de rendimiento? → No, se asume que las funciones ventana son válidas.

**🧩 Casos límite:**

* [x] Departamento con 1 o 2 empleados → se muestra completo.
* [x] Empates en tercer lugar → se muestran todos.
* [x] Todos con mismo salario → todos tienen rango 1.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Calcular el ranking por departamento y filtrar solo los tres primeros.

**📚 Tipo de problema:**
➡️ Ranking con partición (`PARTITION BY`) y función ventana.

---

### 💡 Enfoque 1 — Usando `DENSE_RANK()`

**Idea:**

1. Crear una subconsulta que asigne un **rango** a cada empleado dentro de su departamento.
2. Usar `DENSE_RANK()` con `PARTITION BY departmentId ORDER BY salary DESC`.
3. Filtrar aquellos con `rank <= 3`.
4. Unir con `Department` para mostrar los nombres.

---

**Pseudocódigo SQL:**

```sql
SELECT
    d.name AS Department,
    e.name AS Employee,
    e.salary AS Salary
FROM (
    SELECT
        name,
        salary,
        departmentId,
        DENSE_RANK() OVER (
            PARTITION BY departmentId
            ORDER BY salary DESC
        ) AS rank
    FROM Employee
) e
JOIN Department d
    ON e.departmentId = d.id
WHERE e.rank <= 3;
```

---

### 💡 Alternativa — `ROW_NUMBER()` (sin empates)

Si el entrevistador pide **exactamente tres empleados** por departamento (sin importar empates), se usa `ROW_NUMBER()`.

```sql
SELECT
    d.name AS Department,
    e.name AS Employee,
    e.salary AS Salary
FROM (
    SELECT
        name,
        salary,
        departmentId,
        ROW_NUMBER() OVER (
            PARTITION BY departmentId
            ORDER BY salary DESC
        ) AS rn
    FROM Employee
) e
JOIN Department d ON e.departmentId = d.id
WHERE e.rn <= 3;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Usar `DENSE_RANK()` correctamente con alias descriptivos y formato claro.

**✍️ Consulta SQL Final (versión oficial):**

```sql
SELECT
    d.name AS Department,
    e.name AS Employee,
    e.salary AS Salary
FROM (
    SELECT
        name,
        salary,
        departmentId,
        DENSE_RANK() OVER (
            PARTITION BY departmentId
            ORDER BY salary DESC
        ) AS rank
    FROM Employee
) e
JOIN Department d
    ON e.departmentId = d.id
WHERE e.rank <= 3;
```

**🗣️ Explicación hablada:**

> “Dentro de la subconsulta, calculo un ranking de salarios por departamento usando `DENSE_RANK`.
> Luego filtro solo los empleados con rango 1, 2 o 3, y uno con la tabla de departamentos.
> Esto me da los tres salarios más altos por área, considerando empates.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Confirmar que el ranking y el filtrado funcionan correctamente.

### **Caso de prueba 1 — Empates**

**Employee**

| name  | salary | departmentId |
| ----- | ------ | ------------ |
| Max   | 90000  | 1            |
| Joe   | 85000  | 1            |
| Randy | 85000  | 1            |
| Janet | 69000  | 1            |
| Will  | 70000  | 1            |
| Henry | 80000  | 2            |
| Sam   | 60000  | 2            |

**Department**

| id | name  |
| -- | ----- |
| 1  | IT    |
| 2  | Sales |

**Salida esperada**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |

✅ Correcto.

---

### **Caso de prueba 2 — Solo dos empleados**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| HR         | Alice    | 70000  |
| HR         | Bob      | 50000  |

**Salida esperada:**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| HR         | Alice    | 70000  |
| HR         | Bob      | 50000  |

✅ Correcto — muestra todos porque no hay 3 disponibles.

---

### **Caso de prueba 3 — Todos iguales**

| name | salary | departmentId |
| ---- | ------ | ------------ |
| A    | 50000  | 1            |
| B    | 50000  | 1            |
| C    | 50000  | 1            |

**Salida esperada**

| Department | Employee | Salary |
| ---------- | -------- | ------ |
| X          | A        | 50000  |
| X          | B        | 50000  |
| X          | C        | 50000  |

✅ Todos con rango 1 → incluidos.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                        |
| ---------------------- | -------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N log N) — ordenamiento por partición.     |
| 💾 **Espacio:**        | O(N) — columna calculada `rank`.             |
| ⚡ **Escalabilidad:**   | Excelente para tablas medianas-grandes.      |
| 🧩 **Tipo de patrón:** | Ranking con `DENSE_RANK()` y `PARTITION BY`. |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Comprender el patrón y cómo extenderlo a otros problemas.

**🔧 Posibles optimizaciones:**

* Indexar `departmentId` para mejorar la partición.
* Cambiar `DENSE_RANK()` → `RANK()` si se quieren saltos en posiciones tras empates.
* Cambiar `DENSE_RANK()` → `ROW_NUMBER()` si se quieren exactamente tres filas fijas.

**📚 Lecciones aprendidas:**

* Las funciones ventana son superiores a las subconsultas anidadas en claridad y rendimiento.
* `PARTITION BY` divide el dataset lógicamente en grupos internos.
* `DENSE_RANK` permite empates y evita huecos en el ranking.

**✅ Conclusión final:**

> “Usé `DENSE_RANK()` con `PARTITION BY departmentId ORDER BY salary DESC`
> para asignar rangos por departamento y filtré los tres primeros.
> Este patrón es clave para resolver rankings, top-N y comparaciones dentro de grupos.”

---

📘 **Resumen final**

| Aspecto          | Valor                      |
| ---------------- | -------------------------- |
| Patrón           | Ranking con `DENSE_RANK()` |
| Complejidad      | O(N log N) tiempo          |
| Palabra clave    | “Top-N por grupo”          |
| Tipo de problema | Función ventana SQL        |
| Nivel            | 🟡 Medio                   |

