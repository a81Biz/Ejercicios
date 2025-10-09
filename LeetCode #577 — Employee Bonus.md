# 💰 **LeetCode #577 — Employee Bonus**

> **Tema:** `LEFT JOIN` + condiciones `IS NULL`
> **Nivel:** 🟡 Medio
> **Patrón:** *Detección de registros faltantes o sin coincidencias*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Mostrar los empleados que **no recibieron bono** o **recibieron bono menor a 1000**, junto con su nombre y salario.

---

**📖 Enunciado resumido:**
Se tienen dos tablas:

### 🧩 Tabla 1: `Employee`

| Campo        | Tipo    | Descripción                |
| ------------ | ------- | -------------------------- |
| `empId`      | int     | Identificador del empleado |
| `name`       | varchar | Nombre del empleado        |
| `supervisor` | int     | ID del supervisor          |
| `salary`     | int     | Salario base               |

---

### 🧩 Tabla 2: `Bonus`

| Campo   | Tipo | Descripción                                |
| ------- | ---- | ------------------------------------------ |
| `empId` | int  | Identificador del empleado (clave foránea) |
| `bonus` | int  | Valor del bono recibido                    |

---

**Tarea:**
Devuelve el **nombre y el salario** de todos los empleados que:

* No tienen bono (`NULL`), o
* Tienen un bono menor a `1000`.

---

**🔢 Ejemplo de datos:**

**Employee**

| empId | name   | supervisor | salary |
| ----- | ------ | ---------- | ------ |
| 3     | Brad   | null       | 4000   |
| 1     | John   | 3          | 1000   |
| 2     | Dan    | 3          | 2000   |
| 4     | Thomas | 3          | 4000   |

**Bonus**

| empId | bonus |
| ----- | ----- |
| 2     | 500   |
| 4     | 2000  |

**🎯 Salida esperada:**

| name | salary |
| ---- | ------ |
| Brad | 4000   |
| John | 1000   |
| Dan  | 2000   |

---

**💬 Reexplicación en voz alta:**

> “Debo unir ambas tablas y mostrar a los empleados cuyo bono es nulo o menor que 1000.
> Si el empleado no aparece en la tabla `Bonus`, significa que no recibió nada (es decir, `NULL`).”

---

**❓ Preguntas al entrevistador:**

* ¿Qué pasa si todos tienen bono? → Se muestran solo los menores a 1000.
* ¿Y si nadie tiene bono? → Se muestran todos.
* ¿Hay empleados sin registro en Bonus? → Sí, y deben aparecer.
* ¿Se puede usar `INNER JOIN`? → No, se requiere `LEFT JOIN` para conservar empleados sin bono.

**🧩 Casos límite:**

* [x] Ningún empleado con bono → todos aparecen.
* [x] Bono exactamente igual a 1000 → ❌ no se incluye.
* [x] Bonus negativo o nulo → ✅ incluido.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Unir las tablas y filtrar por ausencia (`NULL`) o valor bajo (`< 1000`).

**📚 Tipo de problema:**
➡️ Unión externa + condición compuesta.

---

### 💡 Enfoque 1 — `LEFT JOIN` + `IS NULL` + `OR`

1. Unir `Employee` con `Bonus` por `empId`.
2. Filtrar empleados sin bono (`bonus IS NULL`) o con bono menor que 1000.
3. Mostrar `name` y `salary`.

---

**Pseudocódigo SQL:**

```sql
SELECT e.name, e.salary
FROM Employee e
LEFT JOIN Bonus b
ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS NULL;
```

---

### 💡 Alternativa — `COALESCE()`

Convertir `NULL` en 0 y filtrar directamente:

```sql
WHERE COALESCE(b.bonus, 0) < 1000;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara y condiciones correctamente agrupadas.

**✍️ Consulta SQL Final:**

```sql
SELECT
    e.name,
    e.salary
FROM
    Employee e
LEFT JOIN
    Bonus b
    ON e.empId = b.empId
WHERE
    b.bonus < 1000 OR b.bonus IS NULL;
```

**🗣️ Explicación hablada:**

> “Uso `LEFT JOIN` para conservar a todos los empleados, incluso los que no tienen bono.
> Luego aplico una condición doble: si el bono es menor que 1000 o si no existe (`NULL`).
> Finalmente, muestro solo el nombre y salario.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Comprobar que la lógica de unión y filtro funciona correctamente.

---

### **Caso de prueba 1 — Mixto**

**Employee**

| empId | name   | salary |
| ----- | ------ | ------ |
| 1     | John   | 1000   |
| 2     | Dan    | 2000   |
| 3     | Brad   | 4000   |
| 4     | Thomas | 4000   |

**Bonus**

| empId | bonus |
| ----- | ----- |
| 2     | 500   |
| 4     | 2000  |

**Salida esperada**

| name | salary |
| ---- | ------ |
| Brad | 4000   |
| John | 1000   |
| Dan  | 2000   |

✅ Correcto.

---

### **Caso de prueba 2 — Todos con bono bajo**

| Bonus |       |
| ----- | ----- |
| empId | bonus |
| 1     | 500   |
| 2     | 800   |

✅ Ambos incluidos (todos < 1000).

---

### **Caso de prueba 3 — Todos con bono alto**

| Bonus |       |
| ----- | ----- |
| empId | bonus |
| 1     | 1500  |
| 2     | 2000  |

✅ Resultado vacío (ninguno cumple condición).

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                |
| ---------------------- | ------------------------------------ |
| ⏱️ **Tiempo:**         | O(N + M) — una unión lineal.         |
| 💾 **Espacio:**        | O(1) — sin agregaciones adicionales. |
| ⚡ **Escalabilidad:**   | Muy buena con índice en `empId`.     |
| 🧩 **Tipo de patrón:** | `LEFT JOIN` con condición compuesta. |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Comprender cómo detectar faltantes o valores condicionales en relaciones externas.

**🔧 Posibles optimizaciones:**

* Usar `COALESCE()` para simplificar condiciones:

  ```sql
  WHERE COALESCE(b.bonus, 0) < 1000;
  ```
* Crear índice en `Bonus.empId` para acelerar la unión.
* En bases grandes, usar `EXISTS` si hay subconsultas más complejas.

**📚 Lecciones aprendidas:**

* `LEFT JOIN` mantiene registros de la izquierda aunque no haya coincidencia.
* `IS NULL` es la forma estándar de detectar faltantes tras la unión.
* Combinando ambas técnicas se resuelven gran parte de los problemas de auditoría y reportes de datos faltantes.

**✅ Conclusión final:**

> “Uní `Employee` y `Bonus` con `LEFT JOIN` y filtré a quienes no tenían bono o tenían menos de 1000.
> Esta técnica permite identificar ausencias o valores bajos dentro de relaciones entre tablas.”

---

📘 **Resumen final**

| Aspecto          | Valor                                |
| ---------------- | ------------------------------------ |
| Patrón           | `LEFT JOIN` + `IS NULL`              |
| Complejidad      | O(N + M) tiempo                      |
| Palabra clave    | “Detectar faltantes o bajos valores” |
| Tipo de problema | Unión con condición                  |
| Nivel            | 🟡 Medio                             |
