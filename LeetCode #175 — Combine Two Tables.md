# 🔗 **LeetCode #175 — Combine Two Tables**

> **Tema:** Uniones (`JOIN`) entre tablas
> **Nivel:** 🟢 Fácil
> **Patrón:** *LEFT JOIN con coincidencia de claves foráneas*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Combinar datos de dos tablas relacionadas para mostrar información completa.

**📖 Enunciado resumido:**
Se te proporcionan dos tablas:

### 🧩 Tabla 1: `Person`

| Campo       | Tipo    | Descripción                 |
| ----------- | ------- | --------------------------- |
| `personId`  | int     | Identificador de la persona |
| `firstName` | varchar | Nombre                      |
| `lastName`  | varchar | Apellido                    |

### 🧩 Tabla 2: `Address`

| Campo       | Tipo    | Descripción                   |
| ----------- | ------- | ----------------------------- |
| `addressId` | int     | Identificador de dirección    |
| `personId`  | int     | ID de persona (clave foránea) |
| `city`      | varchar | Ciudad                        |
| `state`     | varchar | Estado o provincia            |

**Tarea:**
Escribe una consulta que devuelva el **nombre y apellido de cada persona**, junto con su **ciudad y estado**, si existe.
Incluso si la persona no tiene dirección registrada, **debe aparecer en el resultado**.

---

**🎯 Salida esperada (columnas):**

| firstName | lastName | city | state |
| --------- | -------- | ---- | ----- |

---

**💬 Reexplicación en voz alta:**

> “Debo combinar las dos tablas por `personId` y mostrar los datos de la persona,
> junto con su dirección si existe.
> Si no tiene dirección, aún debe aparecer con valores `NULL` en `city` y `state`.”

**❓ Preguntas al entrevistador:**

* ¿Debo mostrar personas sin dirección? → ✅ Sí, eso implica un `LEFT JOIN`.
* ¿Qué pasa si hay más de una dirección? → No se indica, se asume una relación 1:1.
* ¿Se pueden cambiar los nombres de las columnas? → No, deben coincidir con los esperados.
* ¿Se requiere ordenar el resultado? → No, el orden no importa.

**🧩 Casos límite:**

* [x] Persona sin dirección → se muestra con `NULL` en `city`, `state`.
* [x] Persona con dirección → se combina correctamente.
* [x] Tabla `Address` vacía → todas las personas aparecen con `NULL`.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Elegir la unión correcta y definir las columnas específicas.

**📚 Tipo de problema:**
➡️ Unión entre tablas usando clave foránea (`JOIN` por `personId`).

**Estrategia:**

1. Seleccionar las columnas requeridas de ambas tablas.
2. Unir `Person` (principal) con `Address` (secundaria) usando `LEFT JOIN`.
3. Enlazar por `personId`.
4. Mostrar las columnas en el orden solicitado: `firstName`, `lastName`, `city`, `state`.

**Pseudocódigo SQL:**

```sql
SELECT p.firstName, p.lastName, a.city, a.state
FROM Person p
LEFT JOIN Address a
ON p.personId = a.personId;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Expresar la unión de forma explícita, clara y con alias descriptivos.

**✍️ Consulta SQL Final:**

```sql
SELECT
    p.firstName,
    p.lastName,
    a.city,
    a.state
FROM
    Person p
LEFT JOIN
    Address a
ON
    p.personId = a.personId;
```

**🗣️ Explicación hablada:**

> “Uso `LEFT JOIN` porque quiero que todas las personas aparezcan,
> incluso las que no tienen dirección.
> Enlazo las tablas por `personId`, y selecciono solo las columnas necesarias.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Confirmar que el `LEFT JOIN` se comporta correctamente con y sin coincidencias.

### **Caso de prueba 1 — Coincidencias completas**

**Entrada**

**Person**

| personId | firstName | lastName |
| -------- | --------- | -------- |
| 1        | Allen     | Wang     |
| 2        | Bob       | Alice    |

**Address**

| addressId | personId | city     | state |
| --------- | -------- | -------- | ----- |
| 1         | 2        | New York | NY    |

**Consulta**

```sql
SELECT p.firstName, p.lastName, a.city, a.state
FROM Person p
LEFT JOIN Address a
ON p.personId = a.personId;
```

**Salida esperada**

| firstName | lastName | city     | state |
| --------- | -------- | -------- | ----- |
| Allen     | Wang     | NULL     | NULL  |
| Bob       | Alice    | New York | NY    |

✅ Correcto — `Allen` aparece con valores nulos, ya que no tiene dirección.

---

### **Caso de prueba 2 — Todos con dirección**

| Person       | Address           |
| ------------ | ----------------- |
| 1, John, Doe | 1, 1, Chicago, IL |
| 2, Jane, Doe | 2, 2, Miami, FL   |

**Salida esperada**

| firstName | lastName | city    | state |
| --------- | -------- | ------- | ----- |
| John      | Doe      | Chicago | IL    |
| Jane      | Doe      | Miami   | FL    |

✅ Correcto — se combinan perfectamente todas las filas.

---

### **Caso de prueba 3 — Sin direcciones**

**Address** está vacía → todos aparecen con `NULL` en las columnas adicionales.

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                               |
| ---------------------- | --------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N + M) — una sola unión lineal.                   |
| 💾 **Espacio:**        | O(1) — se generan columnas derivadas.               |
| ⚡ **Escalabilidad:**   | Muy alta; JOIN optimizado por índices (`personId`). |
| 🧩 **Tipo de patrón:** | `LEFT JOIN` con relación uno a uno.                 |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Generalizar el patrón para relaciones más complejas.

**🔧 Posibles optimizaciones:**

* Crear índice sobre `Address.personId` para acelerar la unión.
* Cambiar a `INNER JOIN` si se desea excluir personas sin dirección.
* En relaciones 1:N, agregar `GROUP BY` o `DISTINCT` si hay duplicados.

**📚 Lecciones aprendidas:**

* `LEFT JOIN` conserva filas de la tabla izquierda, completando con `NULL` cuando no hay coincidencia.
* Los alias (`p`, `a`) mejoran legibilidad y evitan ambigüedades.
* Los `JOIN` son el núcleo del modelado relacional; entenderlos es esencial para cualquier entrevista SQL.

**✅ Conclusión final:**

> “Uní las tablas `Person` y `Address` usando `LEFT JOIN` sobre `personId`,
> para mostrar todas las personas junto con su ciudad y estado si existen.
> Es una consulta relacional clásica y muy usada en entrevistas.”

---

📘 **Resumen final**

| Aspecto          | Valor                                   |
| ---------------- | --------------------------------------- |
| Patrón           | `LEFT JOIN` entre tablas                |
| Complejidad      | O(N + M) tiempo                         |
| Palabra clave    | “Conservar filas de la tabla izquierda” |
| Tipo de problema | Unión de registros SQL                  |
| Nivel            | 🟢 Fácil                                |
