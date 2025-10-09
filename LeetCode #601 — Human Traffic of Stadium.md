# 🏟️ **LeetCode #601 — Human Traffic of Stadium**

> **Tema:** Funciones ventana y detección de secuencias consecutivas
> **Nivel:** 🔴 Difícil
> **Patrón:** *Identificación de grupos consecutivos que cumplen una condición lógica*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Encontrar períodos consecutivos de **3 o más días**
> donde el número de visitantes (`people`) fue **mayor o igual a 100**.

---

**📖 Enunciado resumido:**
Se tiene una tabla `Stadium` con la siguiente estructura:

| Campo        | Tipo | Descripción                                           |
| ------------ | ---- | ----------------------------------------------------- |
| `id`         | int  | Identificador del registro (incremental, consecutivo) |
| `visit_date` | date | Fecha del registro                                    |
| `people`     | int  | Número de personas que visitaron el estadio ese día   |

---

**Tarea:**
Mostrar **todos los registros (id, visit_date, people)**
que formen parte de una **secuencia de al menos 3 días consecutivos**
donde `people >= 100`.

---

**🔢 Ejemplo de datos:**

| id | visit_date | people |
| -- | ---------- | ------ |
| 1  | 2017-01-01 | 10     |
| 2  | 2017-01-02 | 109    |
| 3  | 2017-01-03 | 150    |
| 4  | 2017-01-04 | 99     |
| 5  | 2017-01-05 | 145    |
| 6  | 2017-01-06 | 150    |
| 7  | 2017-01-07 | 199    |
| 8  | 2017-01-08 | 188    |

**🎯 Salida esperada:**

| id | visit_date | people |
| -- | ---------- | ------ |
| 5  | 2017-01-05 | 145    |
| 6  | 2017-01-06 | 150    |
| 7  | 2017-01-07 | 199    |
| 8  | 2017-01-08 | 188    |

---

**💬 Reexplicación en voz alta:**

> “Debo encontrar secuencias de al menos tres días seguidos
> donde el número de visitantes sea de 100 o más.
> Y mostrar todos los registros que pertenezcan a esas secuencias.”

---

**❓ Preguntas al entrevistador:**

* ¿El campo `id` está siempre en orden cronológico? → ✅ Sí.
* ¿Qué pasa si hay exactamente 3 días? → ✅ Se incluyen.
* ¿Y si hay 4 días seguidos? → ✅ Todos los 4 se muestran.
* ¿Qué ocurre con valores inferiores a 100? → ❌ Rompen la secuencia.

**🧩 Casos límite:**

* [x] Menos de 3 días consecutivos → no se muestran.
* [x] Justo 3 días consecutivos → se muestran.
* [x] Más de 3 días consecutivos → se muestran todos.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Detectar días consecutivos con `people >= 100`
> y agruparlos cuando hay al menos 3 seguidos.

**📚 Tipo de problema:**
➡️ *Detección de secuencias consecutivas* con funciones ventana.

---

### 💡 Enfoque 1 — Agrupación por “diferencia constante” (`id - row_number()`)

Una técnica avanzada:

1. Filtrar solo los días con `people >= 100`.
2. Para cada fila, calcular `id - ROW_NUMBER() OVER (ORDER BY id)` →
   los días consecutivos tendrán el mismo valor de diferencia.
3. Agrupar por esa diferencia.
4. Seleccionar solo los grupos con `COUNT(*) >= 3`.
5. Mostrar todas las filas de esos grupos.

---

**Ejemplo visual:**

| id | people | ROW_NUMBER | id - row_number | Grupo |
| -- | ------ | ---------- | --------------- | ----- |
| 2  | 109    | 1          | 1               | A     |
| 3  | 150    | 2          | 1               | A     |
| 5  | 145    | 3          | 2               | B     |
| 6  | 150    | 4          | 2               | B     |
| 7  | 199    | 5          | 2               | B     |
| 8  | 188    | 6          | 2               | B     |

→ Grupo A tiene 2 filas (no se muestra).
→ Grupo B tiene 4 filas (se muestra).

---

**Pseudocódigo SQL:**

```sql
WITH filtered AS (
    SELECT *
    FROM Stadium
    WHERE people >= 100
),
grouped AS (
    SELECT *,
           id - ROW_NUMBER() OVER (ORDER BY id) AS grp
    FROM filtered
)
SELECT id, visit_date, people
FROM grouped
WHERE grp IN (
    SELECT grp
    FROM grouped
    GROUP BY grp
    HAVING COUNT(*) >= 3
);
```

---

### 💡 Enfoque 2 — `LAG()` / `LEAD()` (más intuitivo)

Comparar directamente los registros anteriores y siguientes.

```sql
SELECT DISTINCT s1.*
FROM Stadium s1
JOIN Stadium s2 ON s1.id = s2.id + 1
JOIN Stadium s3 ON s2.id = s3.id + 1
WHERE s1.people >= 100 AND s2.people >= 100 AND s3.people >= 100;
```

*(Más corto, pero menos generalizable.)*

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Versión moderna y explicable con CTE y funciones ventana.

**✍️ Consulta SQL Final:**

```sql
WITH filtered AS (
    SELECT *
    FROM Stadium
    WHERE people >= 100
),
grouped AS (
    SELECT *,
           id - ROW_NUMBER() OVER (ORDER BY id) AS grp
    FROM filtered
)
SELECT id, visit_date, people
FROM grouped
WHERE grp IN (
    SELECT grp
    FROM grouped
    GROUP BY grp
    HAVING COUNT(*) >= 3
)
ORDER BY visit_date;
```

**🗣️ Explicación hablada:**

> “Primero filtro los días con más de 100 personas.
> Luego calculo un identificador `grp = id - row_number()`
> para detectar secuencias consecutivas.
> Los días consecutivos comparten el mismo grupo.
> Finalmente, selecciono solo los grupos con al menos 3 filas.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Asegurar que detecta correctamente secuencias consecutivas de ≥3.

### **Caso de prueba 1 — Ejemplo base**

✅ Devuelve los días 5, 6, 7, 8 (4 consecutivos).

---

### **Caso de prueba 2 — Solo 2 días consecutivos**

| id | people |
| -- | ------ |
| 1  | 120    |
| 2  | 130    |
| 3  | 90     |

✅ Ninguno → salida vacía.

---

### **Caso de prueba 3 — Secuencia de 3 exactos**

| id | people |
| -- | ------ |
| 10 | 100    |
| 11 | 101    |
| 12 | 102    |
| 13 | 80     |

✅ Devuelve 10, 11, 12.

---

### **Caso de prueba 4 — Múltiples grupos**

| id | people |
| -- | ------ |
| 1  | 100    |
| 2  | 101    |
| 3  | 102    |
| 6  | 150    |
| 7  | 120    |
| 8  | 115    |
| 9  | 99     |

✅ Devuelve los días 1–3 y 6–8.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                             |
| ---------------------- | ------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N log N) — por ventana y agrupación.            |
| 💾 **Espacio:**        | O(N) — para columnas calculadas.                  |
| ⚡ **Escalabilidad:**   | Excelente en SQL modernos (MySQL 8+, PostgreSQL). |
| 🧩 **Tipo de patrón:** | Agrupación por secuencia consecutiva.             |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Entender cómo detectar secuencias consecutivas y extenderlo a otros dominios.

**🔧 Posibles optimizaciones:**

* Crear índice en `id` para acelerar orden y agrupación.
* En MySQL 5.x, reemplazar `ROW_NUMBER()` con variables:

  ```sql
  SELECT id, visit_date, people,
         @grp := id - @rownum AS grp,
         @rownum := @rownum + 1
  FROM Stadium, (SELECT @rownum := 0) r;
  ```
* Adaptar para “eventos consecutivos” (p. ej., usuarios activos, días con ventas, etc.).

**📚 Lecciones aprendidas:**

* `id - ROW_NUMBER()` es un truco poderoso para detectar continuidad.
* Las funciones ventana son ideales para análisis secuencial y detección de patrones.
* Este patrón se aplica a series de tiempo, logs y tráfico web.

**✅ Conclusión final:**

> “Usé `ROW_NUMBER()` para detectar secuencias consecutivas de días con más de 100 visitantes.
> Agrupé por la diferencia constante entre `id` y `ROW_NUMBER()`
> y filtré los grupos con al menos tres días.
> Este patrón generaliza la detección de rachas o patrones temporales consecutivos.”

---

📘 **Resumen final**

| Aspecto          | Valor                                          |
| ---------------- | ---------------------------------------------- |
| Patrón           | Agrupación por secuencia (`id - row_number()`) |
| Complejidad      | O(N log N)                                     |
| Palabra clave    | “Secuencia consecutiva”                        |
| Tipo de problema | Análisis temporal SQL                          |
| Nivel            | 🔴 Difícil                                     |

---

✅ Con esto hemos completado el bloque **Bases de Datos Nivel Difícil (Ejercicios 21–25)**:

| #  | Ejercicio                | Patrón Principal                | Nivel |
| -- | ------------------------ | ------------------------------- | ----- |
| 21 | Trips and Users          | Multi-join + filtro condicional | 🔴    |
| 22 | Managers ≥ 5 Employees   | Self-join + `HAVING`            | 🔴    |
| 23 | Department Top Earners   | Subconsulta correlacionada      | 🔴    |
| 24 | Customers No Transaction | `LEFT JOIN` + `IS NULL`         | 🔴    |
| 25 | Human Traffic of Stadium | `ROW_NUMBER()` + consecutividad | 🔴    |

---
