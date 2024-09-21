Aquí tienes el código para calcular la longitud de cadenas en SQL y MongoDB, utilizando las funciones nativas `LENGTH()` en SQL y `$strLenCP` en MongoDB.

### SQL - Calcular la longitud de una cadena

En SQL, se puede usar la función `LENGTH()` (o `LEN()` en algunos motores como SQL Server) para calcular la longitud de una cadena.

**Ejemplo en SQL (MySQL, PostgreSQL, etc.):**

```sql
SELECT LENGTH('Hola Mundo') AS StringLength;
```

**Salida:**
```
| StringLength |
|--------------|
|      10      |
```

**Explicación:**
- **`LENGTH()`**: Esta función devuelve el número de caracteres en la cadena especificada. Toma en cuenta todos los caracteres, incluyendo espacios.

**Ejemplo en SQL Server (usando `LEN()`):**

```sql
SELECT LEN('Hola Mundo') AS StringLength;
```

**Explicación:**
- **`LEN()`**: Similar a `LENGTH()`, pero se utiliza en SQL Server para calcular la longitud de la cadena.

### MongoDB - Calcular la longitud de una cadena

En MongoDB, puedes utilizar la función de agregación `$strLenCP` para calcular la longitud de una cadena, la cual cuenta los caracteres, considerando los puntos de código UTF-8 correctamente.

**Ejemplo en MongoDB:**

```json
db.collection.aggregate([
  {
    $project: {
      inputString: "$inputField",
      stringLength: { $strLenCP: "$inputField" }
    }
  }
])
```

**Explicación:**
1. **`$project`**: Se utiliza para transformar los documentos del conjunto de resultados, seleccionando los campos que deseas mostrar.
2. **`$strLenCP`**: Esta función calcula la longitud de la cadena en el campo `inputField`, contando los caracteres correctamente según sus puntos de código.

**Ejemplo con datos de entrada:**

Supongamos que tienes una colección con documentos como:

```json
{ "_id": 1, "inputField": "Hola Mundo" }
```

La consulta regresaría:

```json
{ "_id": 1, "inputString": "Hola Mundo", "stringLength": 10 }
```

Ambos ejemplos muestran cómo calcular la longitud de una cadena en sus respectivos entornos de base de datos, utilizando funciones nativas para una evaluación precisa de la longitud de la cadena.