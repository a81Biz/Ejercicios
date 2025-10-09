# 🌍 **LeetCode #595 — Big Countries**

> **Tema:** Filtrado con `WHERE` y operadores lógicos
> **Nivel:** 🟢 Fácil
> **Patrón:** *Selección condicional simple*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender los criterios de filtrado y las columnas relevantes.

**📖 Enunciado resumido:**
La tabla `World` tiene las siguientes columnas:

* `name` (nombre del país)
* `continent` (continente)
* `area` (área en km²)
* `population` (población)
* `gdp` (producto interno bruto)

Debes escribir una consulta que devuelva **el nombre, la población y el área** de los países que cumplan **al menos una** de las siguientes condiciones:

1. `area >= 3000000` (más de 3 millones de km²)
2. `population >= 25000000` (más de 25 millones de habitantes)

**🔢 Ejemplo de datos:**

| name        | continent | area    | population | gdp          |
| ----------- | --------- | ------- | ---------- | ------------ |
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |

**🎯 Salida esperada:**

| name        | population | area    |
| ----------- | ---------- | ------- |
| Afghanistan | 25500100   | 652230  |
| Algeria     | 37100000   | 2381741 |

**💬 Reexplicación en voz alta:**

> “Debo mostrar únicamente los países grandes — aquellos que tengan una gran área o mucha población.
> Si cumplen cualquiera de los dos criterios, aparecen en el resultado.”

**❓ Preguntas al entrevistador:**

* ¿Debo incluir el PIB (`gdp`)? → ❌ No, solo `name`, `population`, `area`.
* ¿Qué pasa si un país cumple ambas condiciones? → ✅ Aparece solo una vez.
* ¿Qué tipo de comparación uso? → Operadores numéricos (`>=`).
* ¿Importa el orden? → No se especifica, el orden por defecto está bien.

**🧩 Casos límite:**

* [x] País sin población ni área → no cumple condiciones.
* [x] País que cumple ambas → se muestra una sola vez.
* [x] Todas las filas vacías → resultado vacío.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Elegir los operadores correctos para expresar la condición compuesta.

**📚 Tipo de problema:**
➡️ Filtrado condicional simple con operador lógico `OR`.

**Estrategia:**

1. Seleccionar las columnas requeridas: `name`, `population`, `area`.
2. Aplicar un filtro con `WHERE` que use `OR` para cubrir ambas condiciones.
3. No es necesario agrupar ni ordenar.

**Pseudocódigo SQL:**

```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Usar una sintaxis clara y legible con nombres de columnas explícitos.

**✍️ Consulta SQL Final:**

```sql
SELECT
    name,
    population,
    area
FROM
    World
WHERE
    area >= 3000000
    OR population >= 25000000;
```

**🗣️ Explicación hablada:**

> “Primero selecciono las columnas relevantes.
> Luego aplico la condición `WHERE` con un `OR`,
> de modo que el país se incluya si cumple **una o ambas** reglas.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Comprobar que el filtro se comporta correctamente con datos variados.

**Caso de prueba 1:**
**Entrada**

| name | continent | area    | population |
| ---- | --------- | ------- | ---------- |
| A    | X         | 5000000 | 1000000    |
| B    | Y         | 200000  | 26000000   |
| C    | Z         | 100000  | 1000000    |

**Consulta:**

```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000;
```

**Salida esperada**

| name | population | area    |
| ---- | ---------- | ------- |
| A    | 1000000    | 5000000 |
| B    | 26000000   | 200000  |

✅ Resultado correcto — incluye los países que cumplen **al menos una condición**.

---

**Caso de prueba 2:**
**Entrada**

| name | area    | population |
| ---- | ------- | ---------- |
| X    | 3100000 | 26000000   |
| Y    | 100000  | 100000     |

**Salida esperada**

| name | population | area    |
| ---- | ---------- | ------- |
| X    | 26000000   | 3100000 |

✅ Correcto — no se duplican los países que cumplen ambas condiciones.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                                                        |
| ---------------------- | ---------------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — un escaneo completo de la tabla.                                      |
| 💾 **Espacio:**        | O(1) — sin operaciones adicionales.                                          |
| ⚡ **Escalabilidad:**   | Excelente; filtros simples en índices (`area`, `population`) son inmediatos. |
| 🧩 **Tipo de patrón:** | Filtro condicional (`WHERE` con `OR`).                                       |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar comprensión y variantes del patrón de filtrado.

**🔧 Posibles optimizaciones:**

* Si la tabla es grande, crear índices sobre `area` y `population`.
* Añadir `ORDER BY population DESC` si se requiere ordenar resultados.
* Reutilizar el patrón `WHERE ... OR ...` para consultas con múltiples umbrales.

**📚 Lecciones aprendidas:**

* `OR` amplía el rango de resultados; `AND` lo restringe.
* La claridad y legibilidad son más importantes que la brevedad en SQL.
* Siempre limitar las columnas a las necesarias mejora rendimiento y claridad.

**✅ Conclusión final:**

> “Seleccioné las columnas requeridas de `World`
> y filtré usando `WHERE` con `OR` para cubrir ambas condiciones.
> La consulta es eficiente, clara y fácilmente demostrable en entrevista.”

---

📘 **Resumen final**

| Aspecto          | Valor                                   |
| ---------------- | --------------------------------------- |
| Patrón           | Filtrado condicional (`WHERE` con `OR`) |
| Complejidad      | O(N) tiempo, O(1) espacio               |
| Palabra clave    | “Seleccionar por múltiples criterios”   |
| Tipo de problema | Selección básica de registros           |
| Nivel            | 🟢 Fácil                                |

---