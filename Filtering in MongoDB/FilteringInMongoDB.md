En MongoDB, puedes realizar **filtrados** utilizando el método `find()` junto con **operadores de comparación**. A continuación, te muestro cómo hacerlo con varios ejemplos aplicados a documentos JSON.

### Ejemplo de Colección: `employees`

Supongamos que tienes una colección llamada `employees` con documentos JSON con la siguiente estructura:

```json
{
    "employee_id": 1,
    "name": "John Doe",
    "salary": 50000,
    "department": "Sales",
    "age": 30
},
{
    "employee_id": 2,
    "name": "Jane Smith",
    "salary": 60000,
    "department": "Engineering",
    "age": 35
},
{
    "employee_id": 3,
    "name": "Sam Wilson",
    "salary": 45000,
    "department": "Marketing",
    "age": 28
}
```

### 1. **Filtrar documentos con un valor exacto**

Supongamos que quieres encontrar a todos los empleados que trabajan en el departamento de "Sales".

```javascript
db.employees.find({ department: "Sales" })
```

- **Explicación**: Filtramos los documentos que tienen el campo `department` con el valor `"Sales"`.

### 2. **Filtrar con operadores de comparación (`$gt`, `$lt`, `$gte`, `$lte`)**

MongoDB proporciona varios operadores de comparación, tales como:

- `$gt`: Mayor que (greater than)
- `$lt`: Menor que (less than)
- `$gte`: Mayor o igual que (greater than or equal to)
- `$lte`: Menor o igual que (less than or equal to)

#### Filtrar empleados con un salario mayor a 50,000:

```javascript
db.employees.find({ salary: { $gt: 50000 } })
```

- **Explicación**: Usamos el operador `$gt` para encontrar a los empleados cuyo `salary` sea mayor que 50,000.

#### Filtrar empleados con una edad menor o igual a 30:

```javascript
db.employees.find({ age: { $lte: 30 } })
```

- **Explicación**: Usamos el operador `$lte` para encontrar empleados cuya `age` (edad) sea menor o igual a 30.

### 3. **Filtrar con múltiples condiciones usando `$and`**

Supongamos que quieres encontrar empleados que trabajen en "Engineering" **y** que tengan un salario mayor o igual a 60,000.

```javascript
db.employees.find({
    $and: [
        { department: "Engineering" },
        { salary: { $gte: 60000 } }
    ]
})
```

- **Explicación**: Usamos `$and` para combinar dos condiciones: los empleados deben pertenecer al departamento "Engineering" **y** tener un salario mayor o igual a 60,000.

> Nota: En MongoDB, las condiciones pasadas en un mismo `find()` ya actúan como un "AND" implícito, por lo que podrías omitir `$and` así:
```javascript
db.employees.find({
    department: "Engineering",
    salary: { $gte: 60000 }
})
```

### 4. **Filtrar con `$or`**

Para buscar empleados que trabajen en el departamento de "Sales" **o** "Marketing":

```javascript
db.employees.find({
    $or: [
        { department: "Sales" },
        { department: "Marketing" }
    ]
})
```

- **Explicación**: Usamos `$or` para encontrar empleados que trabajen en cualquiera de esos dos departamentos.

### 5. **Filtrar con `$in` y `$nin`**

#### Usar `$in` para buscar empleados en los departamentos "Sales" o "Marketing":

```javascript
db.employees.find({
    department: { $in: ["Sales", "Marketing"] }
})
```

- **Explicación**: El operador `$in` busca valores dentro del array proporcionado, similar a la función de `$or`.

#### Usar `$nin` para excluir empleados de ciertos departamentos:

```javascript
db.employees.find({
    department: { $nin: ["Engineering", "Marketing"] }
})
```

- **Explicación**: El operador `$nin` devuelve documentos cuyos valores no están en el array proporcionado.

### 6. **Filtrar con `$exists`**

Si quieres encontrar empleados que tengan el campo `salary` definido:

```javascript
db.employees.find({ salary: { $exists: true } })
```

- **Explicación**: `$exists` devuelve documentos en los que el campo especificado existe o no. En este caso, encontramos documentos que tienen el campo `salary`.

### 7. **Filtrar con `$ne` (diferente de)**

Si quieres encontrar empleados que no pertenezcan al departamento "Sales":

```javascript
db.employees.find({
    department: { $ne: "Sales" }
})
```

- **Explicación**: El operador `$ne` devuelve documentos donde el campo no tiene el valor especificado, en este caso, empleados que no trabajan en "Sales".

### 8. **Filtrar por rangos de valores**

Si necesitas encontrar empleados cuyo salario esté entre 45,000 y 60,000:

```javascript
db.employees.find({
    salary: { $gte: 45000, $lte: 60000 }
})
```

- **Explicación**: Usamos tanto `$gte` (mayor o igual) como `$lte` (menor o igual) para definir un rango de valores y encontrar empleados cuyos salarios estén entre esos valores.

### Resumen de Operadores de Comparación más Comunes:
- `$gt`: Mayor que
- `$lt`: Menor que
- `$gte`: Mayor o igual que
- `$lte`: Menor o igual que
- `$eq`: Igual a
- `$ne`: Diferente de
- `$in`: Dentro de un conjunto de valores
- `$nin`: Fuera de un conjunto de valores
- `$exists`: El campo existe o no

---

Estos ejemplos cubren los casos más comunes de filtrado en MongoDB utilizando `find()` y operadores de comparación, aplicados a documentos JSON. Son útiles para trabajar con bases de datos no relacionales, donde el esquema es flexible y puedes manejar datos de manera dinámica.