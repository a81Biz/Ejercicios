# 📚 **LeetCode #041 — Design a Library Management System**

> **Tema:** Libros, préstamos, devoluciones y control de penalizaciones
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Relaciones muchos-a-muchos (N:M)*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que gestione una **biblioteca**,
> controlando **usuarios, libros, préstamos y devoluciones**,
> con registro de **fechas, penalizaciones** y **disponibilidad de ejemplares**.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **usuarios** (lectores).
* Registrar **libros**, cada uno con **varios ejemplares (copias)**.
* Permitir **prestar y devolver libros**.
* Controlar el **estado** de cada copia (disponible o prestada).
* Calcular **penalizaciones por retraso**.
* Mantener **historial de préstamos** por usuario.

---

**💬 Reexplicación en voz alta:**

> “Voy a diseñar un sistema de biblioteca donde los usuarios pueden pedir prestados libros.
> Cada libro tiene copias identificables.
> El sistema controlará los préstamos activos, las devoluciones y aplicará penalizaciones por retraso.”

---

**❓ Preguntas al entrevistador:**

* ¿Cada libro tiene múltiples copias? → ✅ Sí.
* ¿Hay límite de préstamos por usuario? → 🟡 Sí, configurable.
* ¿Cuántos días se permite tener un libro? → ✅ 7 días por defecto.
* ¿Se aplican multas por retraso? → ✅ Sí, tarifa diaria.

**🧩 Casos límite:**

* [x] Todos los ejemplares de un libro están prestados.
* [x] Usuario intenta pedir más del límite permitido.
* [x] Devolución después de la fecha límite.
* [x] Libro inexistente o usuario no registrado.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Modelar entidades que reflejen la estructura real de una biblioteca
> con múltiples ejemplares, préstamos activos y validación temporal.

---

### 🧩 Clases Principales

| Clase            | Responsabilidad                                | Relaciones                              |
| ---------------- | ---------------------------------------------- | --------------------------------------- |
| `LibraryManager` | Controla usuarios, libros y préstamos.         | Coordina todas las operaciones.         |
| `User`           | Representa un lector.                          | Posee préstamos activos y un historial. |
| `Book`           | Representa un libro con título y autor.        | Contiene varias `Copy`.                 |
| `Copy`           | Representa una copia física de un libro.       | Tiene estado (disponible/prestada).     |
| `Loan`           | Representa un préstamo.                        | Vincula `User` y `Copy`.                |
| `Penalty`        | Calcula y registra penalizaciones por retraso. | Asociada a un `User`.                   |

---

### 🔁 Flujo de Operación

1. Se registran usuarios y libros (con copias).
2. El usuario solicita un préstamo.
3. Si hay copias disponibles, se registra el préstamo con fecha límite.
4. Al devolver, se calcula penalización si hubo retraso.
5. El préstamo pasa al historial del usuario.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|   LibraryManager     |
+----------------------+
| - users, books, loans |
+----------------------+
| + registerUser()     |
| + addBook()          |
| + borrowBook()       |
| + returnBook()       |
+----------------------+
          |
          v
+----------------------+
|        User          |
+----------------------+
| id, name, loans, penalties |
+----------------------+

+----------------------+
|        Book          |
+----------------------+
| title, author, copies |
+----------------------+

+----------------------+
|        Copy          |
+----------------------+
| id, available        |
+----------------------+

+----------------------+
|        Loan          |
+----------------------+
| user, copy, borrow_date, due_date, returned_date |
+----------------------+

+----------------------+
|      Penalty         |
+----------------------+
| user, amount, reason |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar el flujo completo de registro, préstamo, devolución y penalización.

```python
from datetime import datetime, timedelta

class Copy:
    _counter = 1

    def __init__(self):
        self.id = Copy._counter
        Copy._counter += 1
        self.available = True

class Book:
    def __init__(self, title, author, copies=1):
        self.title = title
        self.author = author
        self.copies = [Copy() for _ in range(copies)]

    def get_available_copy(self):
        for copy in self.copies:
            if copy.available:
                return copy
        return None

class Loan:
    def __init__(self, user, copy, borrow_date, due_date):
        self.user = user
        self.copy = copy
        self.borrow_date = borrow_date
        self.due_date = due_date
        self.returned_date = None

    def mark_returned(self):
        self.returned_date = datetime.now()
        self.copy.available = True

    def is_late(self):
        if not self.returned_date:
            return False
        return self.returned_date > self.due_date

class Penalty:
    def __init__(self, user, amount, reason):
        self.user = user
        self.amount = amount
        self.reason = reason
        self.date = datetime.now()

    def __str__(self):
        return f"[{self.date.strftime('%Y-%m-%d')}] ${self.amount:.2f} - {self.reason}"

class User:
    def __init__(self, uid, name):
        self.id = uid
        self.name = name
        self.active_loans = []
        self.history = []
        self.penalties = []

    def add_penalty(self, penalty):
        self.penalties.append(penalty)
        print(f"💸 Penalización aplicada a {self.name}: ${penalty.amount:.2f} ({penalty.reason})")

class LibraryManager:
    def __init__(self, max_loans=3, loan_days=7, daily_fee=2):
        self.users = {}
        self.books = []
        self.loans = []
        self.max_loans = max_loans
        self.loan_days = loan_days
        self.daily_fee = daily_fee

    def register_user(self, uid, name):
        if uid in self.users:
            print("⚠️ Usuario ya existente.")
            return
        user = User(uid, name)
        self.users[uid] = user
        print(f"🧍 Usuario {name} registrado.")
        return user

    def add_book(self, title, author, copies=1):
        b = Book(title, author, copies)
        self.books.append(b)
        print(f"📖 Libro agregado: '{title}' de {author} ({copies} copias)")
        return b

    def borrow_book(self, uid, title):
        user = self.users.get(uid)
        if not user:
            print("🚫 Usuario no encontrado.")
            return
        if len(user.active_loans) >= self.max_loans:
            print("⚠️ Límite de préstamos alcanzado.")
            return
        book = next((b for b in self.books if b.title == title), None)
        if not book:
            print("🚫 Libro no encontrado.")
            return
        copy = book.get_available_copy()
        if not copy:
            print(f"📚 No hay copias disponibles de '{title}'.")
            return
        copy.available = False
        borrow_date = datetime.now()
        due_date = borrow_date + timedelta(days=self.loan_days)
        loan = Loan(user, copy, borrow_date, due_date)
        user.active_loans.append(loan)
        self.loans.append(loan)
        print(f"✅ '{title}' prestado a {user.name}. Devolver antes de {due_date.strftime('%Y-%m-%d')}")

    def return_book(self, uid, title):
        user = self.users.get(uid)
        if not user:
            print("🚫 Usuario no encontrado.")
            return
        loan = next((l for l in user.active_loans if l.copy and l.copy.available is False and l.copy in [c for b in self.books if b.title == title for c in b.copies]), None)
        if not loan:
            print("⚠️ No hay préstamo activo de ese libro.")
            return
        loan.mark_returned()
        user.active_loans.remove(loan)
        user.history.append(loan)
        if loan.is_late():
            days_late = (loan.returned_date - loan.due_date).days
            penalty = Penalty(user, days_late * self.daily_fee, f"Retraso de {days_late} día(s)")
            user.add_penalty(penalty)
        print(f"📚 '{title}' devuelto por {user.name}.")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear biblioteca
lib = LibraryManager()

# Registrar usuarios
u1 = lib.register_user("U1", "Isabel")
u2 = lib.register_user("U2", "Carlos")

# Agregar libros
b1 = lib.add_book("El Castillo Vagabundo", "Diana Wynne Jones", copies=2)
b2 = lib.add_book("La Historia Interminable", "Michael Ende", copies=1)

# Préstamos
lib.borrow_book("U1", "El Castillo Vagabundo")
lib.borrow_book("U1", "La Historia Interminable")
lib.borrow_book("U2", "El Castillo Vagabundo")

# Devolución (simulada con retraso)
loan = u1.active_loans[0]
loan.due_date = datetime.now() - timedelta(days=3)  # vencido
lib.return_book("U1", "El Castillo Vagabundo")
```

**🎯 Salida esperada:**

```
🧍 Usuario Isabel registrado.
🧍 Usuario Carlos registrado.
📖 Libro agregado: 'El Castillo Vagabundo' de Diana Wynne Jones (2 copias)
📖 Libro agregado: 'La Historia Interminable' de Michael Ende (1 copias)
✅ 'El Castillo Vagabundo' prestado a Isabel. Devolver antes de 2025-10-15
✅ 'La Historia Interminable' prestado a Isabel. Devolver antes de 2025-10-15
✅ 'El Castillo Vagabundo' prestado a Carlos. Devolver antes de 2025-10-15
💸 Penalización aplicada a Isabel: $6.00 (Retraso de 3 día(s))
📚 'El Castillo Vagabundo' devuelto por Isabel.
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                              |
| ---------------------- | ------------------------------------------------------------------ |
| ⏱️ **Tiempo:**         | O(U + B) — búsqueda de usuario y libro.                            |
| 💾 **Espacio:**        | O(U + B + L) — usuarios, libros y préstamos.                       |
| ⚡ **Escalabilidad:**   | Alta — admite gestión de múltiples sedes o catálogos distribuidos. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Validación temporal.                   |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Demostrar control completo del ciclo de préstamo, validación y penalización temporal.

**🔧 Posibles optimizaciones:**

* Añadir `ReservationSystem` para colas de espera.
* Integrar `EmailNotification` para avisos de vencimiento.
* Incorporar `CatalogSearch` con filtros por autor o género.

**📚 Lecciones aprendidas:**

* La **composición jerárquica** (Libro → Copias → Préstamos) simplifica control de disponibilidad.
* El manejo de **tiempo y penalización** requiere precisión en las fechas.
* La modularidad permite escalar a sistemas multiusuario o en línea.

**✅ Conclusión final:**

> “Diseñé un sistema de biblioteca con control de usuarios, préstamos, devoluciones y penalizaciones,
> aplicando composición, control temporal y validaciones de disponibilidad.
> El modelo refleja el funcionamiento real de un sistema bibliotecario moderno.”

---

📘 **Resumen final**

| Aspecto          | Valor                                           |
| ---------------- | ----------------------------------------------- |
| Patrón           | Controlador + Composición + Validación temporal |
| Complejidad      | O(U + B)                                        |
| Palabra clave    | “Gestión de préstamos y penalizaciones”         |
| Tipo de problema | Modelado de sistema bibliotecario               |
| Nivel            | 🟡 Medio                                        |

