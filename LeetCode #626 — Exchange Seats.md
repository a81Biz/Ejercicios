# 🔄 **LeetCode #626 — Exchange Seats**

> **Tema:** Manipulación condicional de filas y reordenamiento (`CASE WHEN`)
> **Nivel:** 🟡 Medio
> **Patrón:** *Reordenamiento basado en posición par/impar*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Intercambiar los asientos de los estudiantes adyacentes en una tabla.

---

**📖 Enunciado resumido:**
Tienes una tabla `Seat` con los siguientes campos:

| Campo     | Tipo    | Descripción                                |
| --------- | ------- | ------------------------------------------ |
| `id`      | int     | Número de asiento (posición, empieza en 1) |
| `student` | varchar | Nombre del estudiante sentado ahí          |

**Tarea:**
Intercambiar los nombres de los estudiantes adyacentes en la tabla.

* Si el `id` es **par**, el estudiante debe intercambiarse con el `id` anterior.
* Si el `id` es **impar** y **no hay siguiente estudiante**, permanece igual.

---

**🔢 Ejemplo de datos:**

| id | student |
| -- | ------- |
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |

**🎯 Salida esperada:**

| id | student |
| -- | ------- |
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |

**💬 Reexplicación en voz alta:**

> “Debo intercambiar los asientos de los estudiantes por pares consecutivos.
> Los números pares toman el nombre del anterior, y los impares el del siguiente.
> Si hay un número impar sin par siguiente, se queda igual.”

---

**❓ Preguntas al entrevistador:**

* ¿El `id` siempre es consecutivo y empieza en 1? → ✅ Sí.
* ¿Puede haber una cantidad impar de filas? → ✅ Sí, el último no cambia.
* ¿Puedo usar una función condicional? → ✅ Se espera el uso de `CASE WHEN`.
* ¿Importa el orden de salida? → ✅ Sí, debe ser ordenado por `id`.

**🧩 Casos límite:**

* [x] Solo 1 estudiante → no cambia.
* [x] Número impar de estudiantes → el último se mantiene.
* [x] Todos pares/impares → se comporta correctamente.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Intercambiar los nombres usando `CASE WHEN` y subconsultas condicionales.

**📚 Tipo de problema:**
➡️ Operaciones de fila a fila (`self-join` o expresión condicional).

---

### 💡 Enfoque 1 — `CASE WHEN` con subconsulta `SELECT`

1. Para cada fila:

   * Si `id` es **par**, seleccionar el estudiante con `id - 1`.
   * Si `id` es **impar**, seleccionar el estudiante con `id + 1`.
   * Si no existe (`id + 1` fuera de rango), mantener el mismo nombre.
2. Ordenar por `id`.

---

**Pseudocódigo SQL:**

```sql
SELECT
    id,
    CASE
        WHEN id % 2 = 0 THEN (
            SELECT student FROM Seat WHERE id = s.id - 1
        )
        WHEN id % 2 = 1 AND (
            SELECT COUNT(*) FROM Seat WHERE id = s.id + 1
        ) > 0 THEN (
            SELECT student FROM Seat WHERE id = s.id + 1
        )
        ELSE student
    END AS student
FROM Seat s
ORDER BY id;
```

---

### 💡 Enfoque 2 — `CASE WHEN` con `LEAD()` y `LAG()` *(SQL moderno)*

```sql
SELECT
    id,
    CASE
        WHEN id % 2 = 0 THEN LAG(student) OVER (ORDER BY id)
        WHEN id % 2 = 1 THEN LEAD(student) OVER (ORDER BY id)
    END AS student
FROM Seat
ORDER BY id;
```

*(El último `NULL` se puede reemplazar con el valor original usando `COALESCE` o `CASE` adicional.)*

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara y legible para entornos SQL estándar (MySQL).

**✍️ Consulta SQL Final (versión clásica):**

```sql
SELECT
    s.id,
    CASE
        WHEN s.id % 2 = 0 THEN (
            SELECT student FROM Seat WHERE id = s.id - 1
        )
        WHEN s.id % 2 = 1 AND (
            SELECT COUNT(*) FROM Seat WHERE id = s.id + 1
        ) > 0 THEN (
            SELECT student FROM Seat WHERE id = s.id + 1
        )
        ELSE s.student
    END AS student
FROM Seat s
ORDER BY s.id;
```

**🗣️ Explicación hablada:**

> “Uso `CASE WHEN` para decidir qué estudiante mostrar según el número de asiento.
> Si es par, tomo el anterior (`id - 1`);
> si es impar y existe un siguiente (`id + 1`), tomo ese;
> de lo contrario, dejo el mismo nombre.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Confirmar que los intercambios se realizan correctamente para casos pares e impares.

### **Caso de prueba 1 — Ejemplo del enunciado**

| id | student |
| -- | ------- |
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |

**Salida esperada**

| id | student |
| -- | ------- |
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |

✅ Correcto.

---

### **Caso de prueba 2 — Cantidad par de estudiantes**

| id | student |
| -- | ------- |
| 1  | A       |
| 2  | B       |
| 3  | C       |
| 4  | D       |

**Salida esperada**

| id | student |
| -- | ------- |
| 1  | B       |
| 2  | A       |
| 3  | D       |
| 4  | C       |

✅ Correcto.

---

### **Caso de prueba 3 — Solo un estudiante**

| id | student |
| -- | ------- |
| 1  | Alice   |

**Salida esperada**

| id | student |
| -- | ------- |
| 1  | Alice   |

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                                                          |
| ---------------------- | ------------------------------------------------------------------------------ |
| ⏱️ **Tiempo:**         | O(N²) en versión clásica (subconsultas por fila) / O(N) en versión `LEAD/LAG`. |
| 💾 **Espacio:**        | O(1).                                                                          |
| ⚡ **Escalabilidad:**   | Buena para conjuntos pequeños; para tablas grandes, preferir `LEAD()`/`LAG()`. |
| 🧩 **Tipo de patrón:** | Transformación posicional (fila a fila).                                       |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar comprensión del patrón de intercambio de filas.

**🔧 Posibles optimizaciones:**

* Usar funciones ventana si el motor SQL lo permite (`LAG`, `LEAD`).
* Crear un índice en `id` (si no existe) para que las subconsultas sean instantáneas.
* En MySQL 8+, la versión optimizada sería:

  ```sql
  SELECT
      id,
      CASE
          WHEN id % 2 = 0 THEN LAG(student) OVER (ORDER BY id)
          WHEN id % 2 = 1 THEN LEAD(student) OVER (ORDER BY id)
      END AS student
  FROM Seat
  ORDER BY id;
  ```

**📚 Lecciones aprendidas:**

* `CASE WHEN` puede cambiar el comportamiento por fila sin `UPDATE`.
* Las funciones ventana permiten mirar “adelante” o “atrás” en el conjunto de resultados.
* Este patrón se aplica en ordenamientos dinámicos, agrupaciones alternadas o juegos de posiciones.

**✅ Conclusión final:**

> “Intercambié los estudiantes adyacentes usando condiciones sobre el campo `id`.
> Si es par, toma el anterior; si es impar y tiene siguiente, toma el siguiente;
> el último impar permanece igual.
> Este problema prueba comprensión de operaciones condicionales y manipulación posicional en SQL.”

---

📘 **Resumen final**

| Aspecto          | Valor                                       |
| ---------------- | ------------------------------------------- |
| Patrón           | `CASE WHEN` + subconsulta / función ventana |
| Complejidad      | O(N²) clásica / O(N) moderna                |
| Palabra clave    | “Intercambio posicional”                    |
| Tipo de problema | Transformación condicional SQL              |
| Nivel            | 🟡 Medio                                    |

---

✅ Con esto completamos el bloque **Bases de Datos Nivel Medio (Ejercicios 16–20)**.

| #  | Ejercicio                     | Patrón Principal         | Nivel |
| -- | ----------------------------- | ------------------------ | ----- |
| 16 | Department Highest Salary     | `JOIN` + `MAX`           | 🟡    |
| 17 | Department Top Three Salaries | `DENSE_RANK()`           | 🟡    |
| 18 | Game Play Analysis IV         | `JOIN` + `DATE_ADD`      | 🟡    |
| 19 | Employee Bonus                | `LEFT JOIN` + `IS NULL`  | 🟡    |
| 20 | Exchange Seats                | `CASE WHEN` + `subquery` | 🟡    |
