# 🎬 **LeetCode #620 — Not Boring Movies**

> **Tema:** Filtros condicionales y ordenamiento
> **Nivel:** 🟢 Fácil
> **Patrón:** *Selección + condición lógica + orden descendente*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Aplicar condiciones lógicas y orden de resultados en una tabla.

**📖 Enunciado resumido:**
La tabla `cinema` contiene los siguientes campos:

| Campo         | Tipo    | Descripción                          |
| ------------- | ------- | ------------------------------------ |
| `id`          | int     | Identificador único de la película   |
| `movie`       | varchar | Nombre de la película                |
| `description` | varchar | Puede contener la palabra `"boring"` |
| `rating`      | float   | Calificación de la película          |

Debes escribir una consulta que:

1. Devuelva **todas las columnas** (`*`).
2. Solo muestre películas con:

   * `id` impar (`id % 2 = 1`), **y**
   * `description` diferente de `"boring"`.
3. Ordene los resultados por `rating` en orden descendente (`DESC`).

---

**🔢 Ejemplo de datos:**

| id | movie      | description | rating |
| -- | ---------- | ----------- | ------ |
| 1  | War        | great 3D    | 8.9    |
| 2  | Science    | fiction     | 8.5    |
| 3  | irish      | boring      | 6.2    |
| 4  | Ice song   | Fantacy     | 8.6    |
| 5  | House card | Interesting | 9.1    |

**🎯 Salida esperada:**

| id | movie      | description | rating |
| -- | ---------- | ----------- | ------ |
| 5  | House card | Interesting | 9.1    |
| 1  | War        | great 3D    | 8.9    |

---

**💬 Reexplicación en voz alta:**

> “Tengo que filtrar películas con `id` impar y descripción distinta de ‘boring’,
> y luego ordenarlas por su calificación de mayor a menor.”

**❓ Preguntas al entrevistador:**

* ¿Qué significa “impar”? → cuando `id % 2 = 1`.
* ¿Qué pasa si el campo `description` está vacío o nulo? → se excluye solo si dice `"boring"`.
* ¿El orden es estricto por `rating` descendente? → ✅ Sí.
* ¿Se devuelven todas las columnas? → ✅ Sí, `SELECT *`.

**🧩 Casos límite:**

* [x] No hay películas → resultado vacío.
* [x] Todas aburridas (`boring`) → resultado vacío.
* [x] IDs pares → ninguna incluida.
* [x] Empates de rating → se mantienen ambas, orden indistinto.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Definir los filtros en orden lógico y la prioridad del ordenamiento.

**📚 Tipo de problema:**
➡️ Filtrado + ordenamiento → combinación de `WHERE` y `ORDER BY`.

**Estrategia:**

1. Filtrar por ID impar → `id % 2 = 1`.
2. Excluir películas con `"boring"` → `description <> 'boring'`.
3. Ordenar por `rating DESC`.
4. Seleccionar todas las columnas (`*`).

**Pseudocódigo SQL:**

```sql
SELECT *
FROM cinema
WHERE id % 2 = 1
  AND description <> 'boring'
ORDER BY rating DESC;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Escribir SQL legible y expresivo, usando operadores correctamente.

**✍️ Consulta SQL Final:**

```sql
SELECT
    *
FROM
    cinema
WHERE
    id % 2 = 1
    AND description <> 'boring'
ORDER BY
    rating DESC;
```

**🗣️ Explicación hablada:**

> “Primero selecciono las películas con identificador impar,
> luego elimino las que tienen descripción ‘boring’,
> y finalmente ordeno los resultados por calificación descendente.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Confirmar que los filtros y el orden funcionan correctamente.

**Caso de prueba 1:**
**Entrada**

| id | movie      | description | rating |
| -- | ---------- | ----------- | ------ |
| 1  | War        | great 3D    | 8.9    |
| 2  | Science    | fiction     | 8.5    |
| 3  | irish      | boring      | 6.2    |
| 4  | Ice song   | Fantacy     | 8.6    |
| 5  | House card | Interesting | 9.1    |

**Consulta**

```sql
SELECT * FROM cinema
WHERE id % 2 = 1 AND description <> 'boring'
ORDER BY rating DESC;
```

**Salida esperada**

| id | movie      | description | rating |
| -- | ---------- | ----------- | ------ |
| 5  | House card | Interesting | 9.1    |
| 1  | War        | great 3D    | 8.9    |

✅ Correcto — muestra solo IDs impares, excluye “boring” y ordena por calificación.

---

**Caso de prueba 2:**
**Entrada**

| id | movie | description | rating |
| -- | ----- | ----------- | ------ |
| 1  | A     | boring      | 10     |
| 2  | B     | awesome     | 7      |
| 3  | C     | epic        | 8      |

**Salida esperada**

| id | movie | description | rating |
| -- | ----- | ----------- | ------ |
| 3  | C     | epic        | 8      |

✅ Solo `id=3` cumple las condiciones.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                      |
| ---------------------- | ------------------------------------------ |
| ⏱️ **Tiempo:**         | O(N log N) — ordenamiento domina el costo. |
| 💾 **Espacio:**        | O(1) — sin estructuras adicionales.        |
| ⚡ **Escalabilidad:**   | Excelente en tablas pequeñas o medianas.   |
| 🧩 **Tipo de patrón:** | Filtro múltiple + ordenamiento.            |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Identificar patrones reusables y posibles mejoras.

**🔧 Posibles optimizaciones:**

* Crear índice compuesto sobre `(id, description, rating)` si la tabla es grande.
* Sustituir `<>` por `NOT LIKE 'boring'` si puede haber mayúsculas o variaciones.
* Aplicar paginación (`LIMIT`) si se espera mucho volumen de resultados.

**📚 Lecciones aprendidas:**

* La combinación de `AND` + `OR` define prioridad lógica; el orden de las condiciones importa.
* Siempre verificar las condiciones de igualdad (`=`) vs desigualdad (`<>`).
* `ORDER BY` añade un costo de clasificación (`O(N log N)`), pero mejora legibilidad y análisis.

**✅ Conclusión final:**

> “Filtré películas con ID impar y descripción distinta de ‘boring’,
> ordenando el resultado por rating descendente.
> La consulta demuestra control de filtros múltiples y ordenamiento.”

---

📘 **Resumen final**

| Aspecto          | Valor                            |
| ---------------- | -------------------------------- |
| Patrón           | Filtrado múltiple + ordenamiento |
| Complejidad      | O(N log N) tiempo                |
| Palabra clave    | “Filtrar y ordenar resultados”   |
| Tipo de problema | Selección de registros SQL       |
| Nivel            | 🟢 Fácil                         |
