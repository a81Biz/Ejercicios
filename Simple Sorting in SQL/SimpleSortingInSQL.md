Aquí te muestro cómo usar la cláusula `ORDER BY` en SQL para ordenar datos en tablas tanto en **orden ascendente** como **descendente**, en columnas numéricas y de texto.

### Ejemplo de Tabla: `employees`
Supongamos que tenemos una tabla llamada `employees` con las siguientes columnas:
- `employee_id` (número)
- `name` (texto)
- `salary` (número)
- `department` (texto)

### 1. **Orden Ascendente en una Columna Numérica (`salary`)**

Este ejemplo muestra cómo ordenar los empleados por su salario en **orden ascendente**:

```sql
SELECT employee_id, name, salary, department
FROM employees
ORDER BY salary ASC;
```

- **Explicación**: `ASC` (por defecto) indica orden ascendente. En este caso, los empleados serán listados con el salario más bajo primero y el más alto al final.

### 2. **Orden Descendente en una Columna Numérica (`salary`)**

Este ejemplo muestra cómo ordenar los empleados por salario en **orden descendente** (de mayor a menor):

```sql
SELECT employee_id, name, salary, department
FROM employees
ORDER BY salary DESC;
```

- **Explicación**: `DESC` indica orden descendente. En este caso, los empleados serán listados con el salario más alto primero y el más bajo al final.

### 3. **Orden Ascendente en una Columna de Texto (`name`)**

Este ejemplo muestra cómo ordenar los empleados por su nombre en **orden alfabético ascendente**:

```sql
SELECT employee_id, name, salary, department
FROM employees
ORDER BY name ASC;
```

- **Explicación**: Los nombres serán ordenados alfabéticamente de la `A` a la `Z` (orden ascendente).

### 4. **Orden Descendente en una Columna de Texto (`name`)**

Este ejemplo muestra cómo ordenar los empleados por su nombre en **orden alfabético descendente**:

```sql
SELECT employee_id, name, salary, department
FROM employees
ORDER BY name DESC;
```

- **Explicación**: Los nombres serán ordenados de la `Z` a la `A` (orden descendente).

### 5. **Orden por Múltiples Columnas: Primero por Departamento, Luego por Salario**

Este ejemplo muestra cómo ordenar los empleados por departamento en **orden ascendente** y luego por salario en **orden descendente** dentro de cada departamento:

```sql
SELECT employee_id, name, salary, department
FROM employees
ORDER BY department ASC, salary DESC;
```

- **Explicación**: Primero se agruparán los empleados por departamento en orden ascendente, y dentro de cada departamento, se ordenarán los empleados por su salario en orden descendente.

### 6. **Orden por Columna Numérica y Columna de Texto**

Este ejemplo muestra cómo ordenar los empleados primero por salario en **orden ascendente**, y en caso de empate, por nombre en **orden ascendente**:

```sql
SELECT employee_id, name, salary, department
FROM employees
ORDER BY salary ASC, name ASC;
```

- **Explicación**: Los empleados serán ordenados por salario, y si dos empleados tienen el mismo salario, se ordenarán alfabéticamente por su nombre.

---

### Notas Importantes:
- **ASC**: Orden ascendente (por defecto). No es necesario especificarlo, pero es bueno usarlo para mayor claridad.
- **DESC**: Orden descendente.
- Puedes ordenar por más de una columna, como se muestra en los ejemplos de orden múltiple.
- La cláusula `ORDER BY` siempre debe ir al final de la consulta.

Estos son ejemplos básicos pero poderosos de cómo ordenar datos en una tabla utilizando `ORDER BY` en SQL.