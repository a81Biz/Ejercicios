# ⚖️ **LeetCode #627 — Swap Salary**

> **Tema:** Actualización condicional (`UPDATE` + `CASE WHEN`)
> **Nivel:** 🟢 Fácil
> **Patrón:** *Transformación in-place basada en condiciones*
> **Categoría:** Bases de Datos (SQL)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Modificar datos de una tabla aplicando condiciones lógicas en una misma instrucción.

**📖 Enunciado resumido:**
Tienes una tabla `Salary` con la siguiente estructura:

| Campo    | Tipo    | Descripción                      |
| -------- | ------- | -------------------------------- |
| `id`     | int     | Identificador único del empleado |
| `name`   | varchar | Nombre del empleado              |
| `sex`    | char(1) | Sexo del empleado ('m' o 'f')    |
| `salary` | int     | Salario actual                   |

**Tarea:**
Intercambiar el valor de `sex`:

* Si `sex = 'm'`, cambiarlo a `'f'`.
* Si `sex = 'f'`, cambiarlo a `'m'`.

---

**🔢 Ejemplo de datos:**

| id | name | sex | salary |
| -- | ---- | --- | ------ |
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |

**🎯 Salida esperada:**

| id | name | sex | salary |
| -- | ---- | --- | ------ |
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |

---

**💬 Reexplicación en voz alta:**

> “Debo recorrer toda la tabla y cambiar la columna `sex` según su valor actual,
> pero sin usar múltiples consultas — todo en una sola instrucción `UPDATE`.”

**❓ Preguntas al entrevistador:**

* ¿Solo hay dos valores posibles (‘m’, ‘f’)? → ✅ Sí.
* ¿Debo usar `IF` o `CASE`? → Cualquiera, pero `CASE WHEN` es estándar SQL.
* ¿Se permiten valores nulos o inválidos? → No, solo los válidos existentes.
* ¿Se requiere devolver los resultados? → No, solo actualizar la tabla.

**🧩 Casos límite:**

* [x] Tabla vacía → no hace nada.
* [x] Todos del mismo sexo → todos cambian correctamente.
* [x] Mezcla equilibrada → intercambia sin errores.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Aplicar transformación condicional sobre una columna sin afectar las demás.

**📚 Tipo de problema:**
➡️ Actualización condicional en toda la tabla.

**Estrategia:**

1. Usar `UPDATE` para modificar la tabla `Salary`.
2. Aplicar `CASE WHEN` sobre la columna `sex`:

   * Si `sex = 'm'`, asignar `'f'`.
   * Si `sex = 'f'`, asignar `'m'`.
3. Dejar las demás columnas sin cambios.

**Pseudocódigo SQL:**

```sql
UPDATE Salary
SET sex = CASE
    WHEN sex = 'm' THEN 'f'
    WHEN sex = 'f' THEN 'm'
END;
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Consulta Limpia

> 🎯 **Objetivo:** Sintaxis clara y 100% estándar SQL.

**✍️ Consulta SQL Final:**

```sql
UPDATE
    Salary
SET
    sex = CASE
        WHEN sex = 'm' THEN 'f'
        WHEN sex = 'f' THEN 'm'
    END;
```

**🗣️ Explicación hablada:**

> “Uso `CASE` para evaluar el valor actual de `sex`.
> Si es ‘m’, lo reemplazo por ‘f’, y viceversa.
> Es una operación in-place que actualiza todos los registros en una sola pasada.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar la Consulta

> 🎯 **Objetivo:** Verificar el intercambio correcto de valores.

### **Caso de prueba 1 — Mezcla de sexos**

**Entrada**

| id | name | sex | salary |
| -- | ---- | --- | ------ |
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |

**Consulta**

```sql
UPDATE Salary
SET sex = CASE
    WHEN sex = 'm' THEN 'f'
    WHEN sex = 'f' THEN 'm'
END;
```

**Salida esperada**

| id | name | sex | salary |
| -- | ---- | --- | ------ |
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |

✅ Correcto — todos los valores se intercambian correctamente.

---

### **Caso de prueba 2 — Todos ‘m’**

**Entrada**

| id | name | sex | salary |
| -- | ---- | --- | ------ |
| 1  | X    | m   | 1000   |
| 2  | Y    | m   | 2000   |

**Salida esperada**

| id | name | sex | salary |
| -- | ---- | --- | ------ |
| 1  | X    | f   | 1000   |
| 2  | Y    | f   | 2000   |

✅ Correcto.

---

### **Caso de prueba 3 — Tabla vacía**

No se genera error; la consulta simplemente no modifica filas.
✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                              |
| ---------------------- | ---------------------------------- |
| ⏱️ **Tiempo:**         | O(N) — una actualización por fila. |
| 💾 **Espacio:**        | O(1) — sin estructuras auxiliares. |
| ⚡ **Escalabilidad:**   | Excelente, lineal.                 |
| 🧩 **Tipo de patrón:** | `UPDATE` con `CASE WHEN`.          |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio del patrón `UPDATE + CASE`.

**🔧 Posibles optimizaciones:**

* Si el campo `sex` es binario, podría representarse como `BIT` y hacerse `sex = 1 - sex`.
* En sistemas MySQL, también se puede usar:

  ```sql
  UPDATE Salary SET sex = IF(sex='m', 'f', 'm');
  ```
* Usar transacciones (`BEGIN/COMMIT`) si se actualiza dentro de un proceso mayor.

**📚 Lecciones aprendidas:**

* `CASE WHEN` permite transformar valores en masa sin subconsultas.
* Es una herramienta universal para limpieza de datos.
* Este patrón se extiende a transformaciones categóricas o mapeos complejos (por ejemplo: “junior → middle → senior”).

**✅ Conclusión final:**

> “Actualicé la tabla `Salary` usando `CASE WHEN` para intercambiar valores de ‘m’ y ‘f’.
> La consulta es eficiente, legible y demuestra comprensión de transformaciones condicionales en SQL.”

---

📘 **Resumen final**

| Aspecto          | Valor                                    |
| ---------------- | ---------------------------------------- |
| Patrón           | `UPDATE` con `CASE WHEN`                 |
| Complejidad      | O(N) tiempo                              |
| Palabra clave    | “Transformar valores en una sola pasada” |
| Tipo de problema | Actualización condicional SQL            |
| Nivel            | 🟢 Fácil                                 |

---

✅ Con esto, completamos el bloque de **Bases de Datos Nivel Fácil (Ejercicios 11–15)**.

| #  | Ejercicio          | Patrón Principal        | Nivel |
| -- | ------------------ | ----------------------- | ----- |
| 11 | Big Countries      | `WHERE` + `OR`          | 🟢    |
| 12 | Not Boring Movies  | `AND` + `ORDER BY`      | 🟢    |
| 13 | Combine Two Tables | `LEFT JOIN`             | 🟢    |
| 14 | Sales Person       | `NOT IN` / `NOT EXISTS` | 🟢    |
| 15 | Swap Salary        | `UPDATE` + `CASE`       | 🟢    |
