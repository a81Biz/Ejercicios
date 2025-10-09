# 🎮 **LeetCode #550 — Game Play Analysis IV**

> **Tema:** Funciones ventana y análisis temporal (`LAG`, `DATEDIFF`)
> **Nivel:** 🟡 Medio
> **Patrón:** *Retención de usuarios basada en actividad secuencial*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Calcular el porcentaje de usuarios que regresaron al juego el día siguiente de su registro.

---

**📖 Enunciado resumido:**
Tienes una tabla `Activity` con los siguientes campos:

| Campo          | Tipo | Descripción                         |
| -------------- | ---- | ----------------------------------- |
| `player_id`    | int  | Identificador del jugador           |
| `device_id`    | int  | ID del dispositivo usado            |
| `event_date`   | date | Fecha del evento (inicio de sesión) |
| `games_played` | int  | Número de partidas jugadas ese día  |

---

**Tarea:**
Calcula el **porcentaje de jugadores** que regresaron y jugaron **exactamente al día siguiente** después de su primer inicio de sesión.

* “Primer inicio de sesión” = fecha más temprana (`MIN(event_date)`) por jugador.
* “Regresó al día siguiente” = existe un `event_date` = `primer_día + 1`.

---

**🔢 Ejemplo de datos:**

| player_id | device_id | event_date | games_played |
| --------- | --------- | ---------- | ------------ |
| 1         | 2         | 2021-01-01 | 5            |
| 1         | 2         | 2021-01-02 | 6            |
| 2         | 3         | 2021-01-01 | 1            |
| 2         | 3         | 2021-01-03 | 6            |
| 3         | 1         | 2021-01-01 | 0            |
| 3         | 1         | 2021-01-02 | 3            |

**🎯 Salida esperada:**

| fraction |
| -------- |
| 0.6667   |

**Explicación:**

* Jugador 1 → Primer día: 01-01 → Jugó el 02-01 ✅
* Jugador 2 → Primer día: 01-01 → Jugó el 03-01 ❌
* Jugador 3 → Primer día: 01-01 → Jugó el 02-01 ✅
  → 2 de 3 regresaron → 2 / 3 = 0.6667

---

**💬 Reexplicación en voz alta:**

> “Por cada jugador, debo identificar su primer día de actividad y ver si regresó exactamente al día siguiente.
> Luego calculo el porcentaje de los que sí lo hicieron.”

---

**❓ Preguntas al entrevistador:**

* ¿Qué significa ‘regresó’? → Que tiene un `event_date` = `primer_día + 1`.
* ¿Se cuentan varios inicios? → No, solo importa el primero y si vuelve el día siguiente.
* ¿Qué pasa si un jugador no vuelve nunca? → No cuenta.
* ¿Qué precisión requiere la fracción? → 4 decimales (`ROUND(..., 4)`).

**🧩 Casos límite:**

* [x] Un solo jugador → devuelve 0 o 1.
* [x] Jugador con actividad discontinua → no cuenta.
* [x] Fecha duplicada → no afecta el conteo.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Calcular la fecha mínima por jugador y verificar si hay actividad al día siguiente.

**📚 Tipo de problema:**
➡️ Funciones de agregación + comparación temporal.

---

### 💡 Enfoque 1 — Subconsulta con `MIN()` y `DATE_ADD()`

1. Calcular el primer día de actividad por jugador (`MIN(event_date)`).
2. Revisar si el jugador tiene un evento en la fecha `primer_día + 1`.
3. Contar jugadores que cumplen la condición.
4. Dividir entre el total de jugadores.

---

**Pseudocódigo SQL:**

```sql
SELECT
    ROUND(
        COUNT(DISTINCT a1.player_id) / COUNT(DISTINCT b.player_id),
        4
    ) AS fraction
FROM Activity a1
JOIN (
    SELECT player_id, MIN(event_date) AS first_login
    FROM Activity
    GROUP BY player_id
) b
ON a1.player_id = b.player_id
AND a1.event_date = DATE_ADD(b.first_login, INTERVAL 1 DAY);
```

---

### 💡 Alternativa — `EXISTS`

```sql
SELECT
    ROUND(
        SUM(
            CASE
                WHEN EXISTS (
                    SELECT 1
                    FROM Activity a2
                    WHERE a2.player_id = a1.player_id
                    AND a2.event_date = DATE_ADD(MIN(a1.event_date), INTERVAL 1 DAY)
                ) THEN 1 ELSE 0 END
        ) / COUNT(DISTINCT a1.player_id), 4
    ) AS fraction
FROM Activity a1;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara, con alias coherentes y cálculo paso a paso.

**✍️ Consulta SQL Final (versión estándar):**

```sql
SELECT
    ROUND(
        COUNT(DISTINCT a.player_id) / COUNT(DISTINCT b.player_id),
        4
    ) AS fraction
FROM
    Activity a
JOIN
    (
        SELECT player_id, MIN(event_date) AS first_login
        FROM Activity
        GROUP BY player_id
    ) b
ON
    a.player_id = b.player_id
    AND a.event_date = DATE_ADD(b.first_login, INTERVAL 1 DAY);
```

**🗣️ Explicación hablada:**

> “Primero encuentro la fecha mínima (`first_login`) por jugador.
> Luego uno esta tabla con la actividad original para ver quién jugó justo el día siguiente (`first_login + 1`).
> Finalmente, divido el número de jugadores que regresaron entre el total, y redondeo a 4 decimales.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Verificar que la relación de jugadores se calcula correctamente.

### **Caso de prueba 1 — Ejemplo del enunciado**

| player_id | event_date |
| --------- | ---------- |
| 1         | 2021-01-01 |
| 1         | 2021-01-02 |
| 2         | 2021-01-01 |
| 2         | 2021-01-03 |
| 3         | 2021-01-01 |
| 3         | 2021-01-02 |

**Salida esperada:**

| fraction |
| -------- |
| 0.6667   |

✅ Correcto.

---

### **Caso de prueba 2 — Un solo jugador**

| player_id | event_date |
| --------- | ---------- |
| 10        | 2021-05-01 |
| 10        | 2021-05-02 |

✅ `1 / 1 = 1.0000`

---

### **Caso de prueba 3 — Ninguno vuelve**

| player_id | event_date |
| --------- | ---------- |
| 1         | 2021-01-01 |
| 2         | 2021-02-01 |

✅ `0 / 2 = 0.0000`

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                                                    |
| ---------------------- | ------------------------------------------------------------------------ |
| ⏱️ **Tiempo:**         | O(N log N) — agrupación + unión.                                         |
| 💾 **Espacio:**        | O(U) — una fila por jugador.                                             |
| ⚡ **Escalabilidad:**   | Excelente; índices sobre `(player_id, event_date)` aceleran la consulta. |
| 🧩 **Tipo de patrón:** | Agregación temporal + comparación de fechas.                             |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Generalizar el patrón de retención y rendimiento temporal.

**🔧 Posibles optimizaciones:**

* Usar `EXISTS` en lugar de `JOIN` si hay gran volumen.
* En PostgreSQL: `LAG()` puede simplificar el cálculo:

  ```sql
  SELECT ROUND(
      SUM(CASE WHEN DATEDIFF(event_date, LAG(event_date) OVER (PARTITION BY player_id ORDER BY event_date)) = 1 THEN 1 ELSE 0 END)
      / COUNT(DISTINCT player_id),
      4
  ) AS fraction
  FROM Activity;
  ```
* Crear índice compuesto `(player_id, event_date)`.

**📚 Lecciones aprendidas:**

* `DATE_ADD()` y `DATEDIFF()` son esenciales en análisis temporal.
* Calcular eventos relativos a un punto inicial (como “primer login”) es una base de métricas de retención.
* Este patrón es fundamental para *cohort analysis* o *customer churn tracking*.

**✅ Conclusión final:**

> “Calculo la fracción de usuarios que regresaron el día siguiente a su primer login.
> Usé una subconsulta para encontrar la primera fecha por jugador y uní con la actividad del día siguiente.
> Este patrón es la base del análisis de retención y comportamiento temporal.”

---

📘 **Resumen final**

| Aspecto          | Valor                             |
| ---------------- | --------------------------------- |
| Patrón           | Agregación + comparación temporal |
| Complejidad      | O(N log N) tiempo                 |
| Palabra clave    | “Retención de usuarios”           |
| Tipo de problema | Análisis de eventos por fecha     |
| Nivel            | 🟡 Medio                          |

---