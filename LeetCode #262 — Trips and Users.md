# 🧮 **LeetCode #262 — Trips and Users**

> **Tema:** Subconsultas correlacionadas + `JOIN` múltiple + filtrado condicional
> **Nivel:** 🔴 Difícil
> **Patrón:** *Análisis condicional multi-tabla con filtros de estado y fecha*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Calcular la **tasa de cancelación diaria** de viajes,
> considerando solo usuarios activos (conductores y pasajeros que no están bloqueados).

---

**📖 Enunciado resumido:**
Se tienen dos tablas: `Trips` y `Users`.

### 🧩 Tabla 1: `Trips`

| Campo        | Tipo | Descripción                                                                  |
| ------------ | ---- | ---------------------------------------------------------------------------- |
| `id`         | int  | Identificador del viaje                                                      |
| `client_id`  | int  | ID del usuario que solicitó el viaje                                         |
| `driver_id`  | int  | ID del conductor                                                             |
| `city_id`    | int  | ID de la ciudad                                                              |
| `status`     | enum | Estado del viaje (`completed`, `cancelled_by_driver`, `cancelled_by_client`) |
| `request_at` | date | Fecha de la solicitud                                                        |

---

### 🧩 Tabla 2: `Users`

| Campo      | Tipo                                | Descripción                         |
| ---------- | ----------------------------------- | ----------------------------------- |
| `users_id` | int                                 | Identificador del usuario           |
| `banned`   | enum('Yes', 'No')                   | Indica si el usuario está bloqueado |
| `role`     | enum('client', 'driver', 'partner') | Rol del usuario                     |

---

**Tarea:**
Para cada fecha entre `'2013-10-01'` y `'2013-10-03'`,
calcular la **tasa de cancelación (cancellation rate)**, definida como:

[
\text{cancel_rate} = \frac{\text{# viajes cancelados}}{\text{# viajes totales}}
]

Solo se consideran:

* Viajes solicitados por **clientes activos** (`banned = 'No'`), y
* Con **conductores activos** (`banned = 'No'`).

---

**🔢 Ejemplo de datos:**

**Trips**

| id | client_id | driver_id | city_id | status              | request_at |
| -- | --------- | --------- | ------- | ------------------- | ---------- |
| 1  | 1         | 10        | 1       | completed           | 2013-10-01 |
| 2  | 2         | 11        | 1       | cancelled_by_driver | 2013-10-01 |
| 3  | 3         | 12        | 6       | completed           | 2013-10-02 |
| 4  | 4         | 13        | 6       | cancelled_by_client | 2013-10-02 |
| 5  | 1         | 10        | 1       | completed           | 2013-10-03 |
| 6  | 2         | 11        | 6       | completed           | 2013-10-03 |
| 7  | 3         | 12        | 6       | completed           | 2013-10-03 |
| 8  | 4         | 13        | 6       | completed           | 2013-10-03 |

**Users**

| users_id | banned | role   |
| -------- | ------ | ------ |
| 1        | No     | client |
| 2        | Yes    | client |
| 3        | No     | client |
| 4        | No     | client |
| 10       | No     | driver |
| 11       | No     | driver |
| 12       | No     | driver |
| 13       | No     | driver |

**🎯 Salida esperada:**

| Day        | Cancellation Rate |
| ---------- | ----------------- |
| 2013-10-01 | 0.00              |
| 2013-10-02 | 0.50              |
| 2013-10-03 | 0.00              |

---

**💬 Reexplicación en voz alta:**

> “Tengo que calcular, por fecha, el porcentaje de viajes cancelados,
> considerando solo los viajes donde tanto el cliente como el conductor están activos (no bloqueados).”

---

**❓ Preguntas al entrevistador:**

* ¿Se incluyen los viajes completados en el total? → ✅ Sí.
* ¿Qué estados se consideran “cancelados”? → `cancelled_by_driver` y `cancelled_by_client`.
* ¿Qué pasa si no hubo viajes ese día? → No aparece.
* ¿Debe redondearse? → Sí, a **dos decimales**.

**🧩 Casos límite:**

* [x] Todos los usuarios bloqueados → no se muestran resultados.
* [x] Fechas sin viajes → se omiten.
* [x] Solo viajes cancelados → tasa = 1.00.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Filtrar viajes válidos, agrupar por día y calcular la fracción cancelada.

**📚 Tipo de problema:**
➡️ Agregación multi-tabla + condición correlacionada.

---

### 💡 Enfoque 1 — `JOIN` doble y agregación condicional

1. Unir `Trips` con `Users` (dos veces):

   * Una para el cliente.
   * Otra para el conductor.
2. Filtrar solo donde ambos `banned = 'No'`.
3. Agrupar por `request_at`.
4. Calcular:

   * `COUNT(*)` = total de viajes.
   * `SUM(status IN ('cancelled_by_driver','cancelled_by_client'))` = cancelados.
5. Dividir y redondear.

---

**Pseudocódigo SQL:**

```sql
SELECT
    t.request_at AS Day,
    ROUND(
        SUM(
            CASE
                WHEN t.status IN ('cancelled_by_driver', 'cancelled_by_client') THEN 1
                ELSE 0
            END
        ) / COUNT(*),
        2
    ) AS 'Cancellation Rate'
FROM Trips t
JOIN Users c ON t.client_id = c.users_id AND c.banned = 'No'
JOIN Users d ON t.driver_id = d.users_id AND d.banned = 'No'
WHERE t.request_at BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY t.request_at
ORDER BY t.request_at;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara y completamente funcional (MySQL estándar).

**✍️ Consulta SQL Final:**

```sql
SELECT
    t.request_at AS Day,
    ROUND(
        SUM(
            CASE
                WHEN t.status IN ('cancelled_by_driver', 'cancelled_by_client') THEN 1
                ELSE 0
            END
        ) / COUNT(*),
        2
    ) AS 'Cancellation Rate'
FROM
    Trips t
JOIN Users c
    ON t.client_id = c.users_id AND c.banned = 'No'
JOIN Users d
    ON t.driver_id = d.users_id AND d.banned = 'No'
WHERE
    t.request_at BETWEEN '2013-10-01' AND '2013-10-03'
GROUP BY
    t.request_at
ORDER BY
    t.request_at;
```

**🗣️ Explicación hablada:**

> “Uno las tablas `Trips`, `Users` (cliente) y `Users` (conductor), filtrando solo usuarios activos.
> Luego agrupo por fecha y calculo la fracción de viajes cancelados dividiendo cancelados entre totales.
> Finalmente redondeo el resultado a dos decimales.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Confirmar que los filtros y la tasa se calculan correctamente.

### **Caso de prueba 1 — Ejemplo base**

✅ `2013-10-01 → 0.00`
✅ `2013-10-02 → 0.50`
✅ `2013-10-03 → 0.00`

---

### **Caso de prueba 2 — Todos cancelados**

| status              | request_at |
| ------------------- | ---------- |
| cancelled_by_client | 2021-01-01 |
| cancelled_by_driver | 2021-01-01 |

**Resultado:**

| Day        | Cancellation Rate |
| ---------- | ----------------- |
| 2021-01-01 | 1.00              |

---

### **Caso de prueba 3 — Usuarios bloqueados**

Si el `client_id` pertenece a un usuario `banned = 'Yes'`,
→ ese viaje no se cuenta en absoluto.
✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                               |
| ---------------------- | --------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N log N) — por agrupación y filtros.              |
| 💾 **Espacio:**        | O(D) — una fila por día.                            |
| ⚡ **Escalabilidad:**   | Excelente con índices en `request_at` y `users_id`. |
| 🧩 **Tipo de patrón:** | Filtro condicional + agregación multi-join.         |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de análisis multi-tabla y condiciones compuestas.

**🔧 Posibles optimizaciones:**

* Crear índices en `Trips(request_at)` y `Users(users_id, banned)`.
* Reemplazar `IN` por `CASE` si el motor SQL lo optimiza mejor.
* Usar CTE para claridad (MySQL 8+):

  ```sql
  WITH ActiveTrips AS (
      SELECT *
      FROM Trips t
      JOIN Users c ON t.client_id = c.users_id AND c.banned = 'No'
      JOIN Users d ON t.driver_id = d.users_id AND d.banned = 'No'
  )
  SELECT
      request_at AS Day,
      ROUND(SUM(status IN ('cancelled_by_driver','cancelled_by_client')) / COUNT(*), 2) AS 'Cancellation Rate'
  FROM ActiveTrips
  GROUP BY request_at;
  ```

**📚 Lecciones aprendidas:**

* Es vital entender qué registros excluir antes de calcular métricas.
* Agrupar con filtros condicionales muestra comprensión de lógica empresarial.
* Este patrón se usa en *reportes de retención, cancelación, churn y calidad de servicio.*

**✅ Conclusión final:**

> “Calculo la tasa de cancelación diaria de viajes válidos (solo clientes y conductores activos),
> agrupando por fecha y aplicando un filtro condicional sobre el estado.
> Este patrón es clave para métricas operativas en sistemas de transporte o delivery.”

---

📘 **Resumen final**

| Aspecto          | Valor                                     |
| ---------------- | ----------------------------------------- |
| Patrón           | `JOIN` múltiple + condición condicional   |
| Complejidad      | O(N log N) tiempo                         |
| Palabra clave    | “Cancelación filtrada por usuario activo” |
| Tipo de problema | Métricas operativas multi-tabla           |
| Nivel            | 🔴 Difícil                                |

---