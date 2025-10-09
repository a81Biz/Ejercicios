# 📚 **LeetCode #029 — Design a Library Management System**

> **Tema:** Modelado de entidades con relaciones uno-a-muchos y control de usuarios
> **Nivel:** 🟡 Medio
> **Patrón:** *Entidad-Controlador + Composición + Reglas de negocio*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que gestione los préstamos, devoluciones y control de libros de una biblioteca, manteniendo la trazabilidad de usuarios y ejemplares.

---

**📖 Enunciado resumido:**
Se requiere un sistema que:

* Registre **libros**, **usuarios** (lectores) y **préstamos activos**.
* Permita **prestar y devolver** libros.
* Verifique disponibilidad antes de cada préstamo.
* Calcule multas o penalizaciones por demora.
* Sea extensible a múltiples sucursales o administradores.

---

**💬 Reexplicación en voz alta:**

> “Necesito modelar una biblioteca donde hay libros (algunos con varias copias),
> usuarios que pueden pedirlos prestados y un controlador que maneja las reglas.
> Los objetos `Library`, `Book`, `User` y `Loan` se relacionarán jerárquicamente.”

---

**❓ Preguntas al entrevistador:**

* ¿Cada libro puede tener múltiples copias? → ✅ Sí.
* ¿Debe incluir cálculo de multas? → 🟡 Opcional.
* ¿Hay límite de préstamos por usuario? → ✅ Sí, configurable.
* ¿Se requiere autenticación o solo modelado lógico? → 💬 Solo modelado.

**🧩 Casos límite:**

* [x] Usuario intenta tomar un libro sin copias disponibles.
* [x] Devolución de libro no prestado.
* [x] Límite de préstamos alcanzado.
* [x] Usuario penalizado no puede tomar nuevos libros.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Definir entidades y sus relaciones naturales: libros ↔ usuarios ↔ préstamos.

---

### 🧩 Clases Principales

| Clase      | Responsabilidad                                   | Relaciones                           |
| ---------- | ------------------------------------------------- | ------------------------------------ |
| `Library`  | Gestiona el inventario y coordina préstamos.      | Contiene `BookCopy`, `User`, `Loan`. |
| `Book`     | Define el título general del libro (autor, ISBN). | Relacionado con `BookCopy`.          |
| `BookCopy` | Representa una copia física del libro.            | Usada por `Loan`.                    |
| `User`     | Representa al lector registrado.                  | Posee múltiples `Loan`.              |
| `Loan`     | Representa un préstamo activo o histórico.        | Une `User` y `BookCopy`.             |

---

### 🔁 Flujo de Operación

1. El usuario se registra en la biblioteca.
2. Solicita un libro.
3. Si hay una copia disponible → se crea un `Loan`.
4. Al devolverlo → el `Loan` se marca como cerrado.
5. Si hay retraso → se calcula multa (opcional).

---

### 🧱 Relaciones UML (simplificadas)

```
+------------------+
|     Library      |
+------------------+
| - books          |
| - users          |
| - loans          |
+------------------+
| + addBook()      |
| + registerUser() |
| + borrowBook()   |
| + returnBook()   |
+------------------+
      | uses
      v
+------------------+
|      User        |
+------------------+
| id, name, limit  |
+------------------+
| + borrow()       |
| + returnBook()   |
+------------------+

+------------------+
|      Book        |
+------------------+
| isbn, title, author |
+------------------+
| + createCopy()   |
+------------------+

+------------------+
|    BookCopy      |
+------------------+
| id, available    |
+------------------+

+------------------+
|      Loan        |
+------------------+
| user, bookCopy, startDate, endDate |
+------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar un modelo que gestione préstamos y devoluciones.

```python
from datetime import datetime, timedelta

class Book:
    def __init__(self, isbn, title, author):
        self.isbn = isbn
        self.title = title
        self.author = author
        self.copies = []

    def create_copy(self, copy_id):
        copy = BookCopy(copy_id, self)
        self.copies.append(copy)
        return copy


class BookCopy:
    def __init__(self, copy_id, book):
        self.copy_id = copy_id
        self.book = book
        self.available = True


class User:
    def __init__(self, user_id, name, borrow_limit=3):
        self.user_id = user_id
        self.name = name
        self.borrow_limit = borrow_limit
        self.active_loans = []

    def can_borrow(self):
        return len(self.active_loans) < self.borrow_limit


class Loan:
    def __init__(self, user, book_copy):
        self.user = user
        self.book_copy = book_copy
        self.start_date = datetime.now()
        self.end_date = None

    def close(self):
        self.end_date = datetime.now()


class Library:
    def __init__(self):
        self.books = {}
        self.users = {}
        self.loans = []

    def add_book(self, isbn, title, author, num_copies=1):
        book = Book(isbn, title, author)
        for i in range(num_copies):
            book.create_copy(f"{isbn}-{i+1}")
        self.books[isbn] = book

    def register_user(self, user_id, name):
        self.users[user_id] = User(user_id, name)

    def borrow_book(self, user_id, isbn):
        user = self.users.get(user_id)
        if not user or not user.can_borrow():
            print(f"El usuario {user_id} no puede tomar más libros.")
            return

        book = self.books.get(isbn)
        if not book:
            print("Libro no encontrado.")
            return

        available_copy = next((c for c in book.copies if c.available), None)
        if not available_copy:
            print("No hay copias disponibles.")
            return

        available_copy.available = False
        loan = Loan(user, available_copy)
        user.active_loans.append(loan)
        self.loans.append(loan)
        print(f"{user.name} tomó prestado '{book.title}' (copia {available_copy.copy_id})")

    def return_book(self, user_id, copy_id):
        user = self.users.get(user_id)
        if not user:
            print("Usuario no encontrado.")
            return

        loan = next((l for l in user.active_loans if l.book_copy.copy_id == copy_id), None)
        if not loan:
            print("Préstamo no encontrado.")
            return

        loan.close()
        loan.book_copy.available = True
        user.active_loans.remove(loan)
        print(f"{user.name} devolvió '{loan.book_copy.book.title}'")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear biblioteca
lib = Library()
lib.add_book("978-0001", "El Hobbit", "J.R.R. Tolkien", num_copies=2)
lib.add_book("978-0002", "1984", "George Orwell", num_copies=1)
lib.register_user("U1", "Isabel")
lib.register_user("U2", "Carlos")

# Préstamos
lib.borrow_book("U1", "978-0001")  # Éxito
lib.borrow_book("U1", "978-0001")  # Segunda copia
lib.borrow_book("U1", "978-0001")  # Sin copias

# Devolución
lib.return_book("U1", "978-0001-1")
```

**🎯 Salida esperada:**

```
Isabel tomó prestado 'El Hobbit' (copia 978-0001-1)
Isabel tomó prestado 'El Hobbit' (copia 978-0001-2)
No hay copias disponibles.
Isabel devolvió 'El Hobbit'
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                             |
| ---------------------- | ----------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(1) para búsquedas, O(N) para disponibilidad.                    |
| 💾 **Espacio:**        | O(U + B + L) — usuarios, libros, préstamos.                       |
| ⚡ **Escalabilidad:**   | Alta — se pueden agregar ramas, administradores y control remoto. |
| 🧩 **Tipo de patrón:** | Controlador + Entidades compuestas.                               |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de relaciones uno-a-muchos y reglas de negocio encapsuladas.

**🔧 Posibles optimizaciones:**

* Clase `FineCalculator` para multas.
* `Admin` para controlar el inventario.
* Uso de base de datos o persistencia de objetos.

**📚 Lecciones aprendidas:**

* Cada entidad debe reflejar una parte del dominio real.
* Las relaciones (usuario-libro-préstamo) deben controlarse desde el nivel superior (`Library`).
* Un buen diseño OOP imita el flujo real de interacción del mundo físico.

**✅ Conclusión final:**

> “Modelé un sistema de biblioteca donde los usuarios, libros y préstamos
> interactúan bajo reglas de disponibilidad y límites.
> El diseño refleja el dominio real, separa responsabilidades y es fácilmente extensible.”

---

📘 **Resumen final**

| Aspecto          | Valor                              |
| ---------------- | ---------------------------------- |
| Patrón           | Controlador + Entidades compuestas |
| Complejidad      | O(N)                               |
| Palabra clave    | “Relaciones uno-a-muchos”          |
| Tipo de problema | Modelado de sistema de gestión     |
| Nivel            | 🟡 Medio                           |
