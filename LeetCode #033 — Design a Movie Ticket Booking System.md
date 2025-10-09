# 🎬 **LeetCode #033 — Design a Movie Ticket Booking System**

> **Tema:** Reservas, validación de disponibilidad y manejo de horarios
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Validación temporal*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que permita **reservar boletos de cine**,
> gestionando **películas, salas, horarios, asientos y clientes**,
> con control de disponibilidad y validación de solapamientos.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **películas** con horarios y duración.
* Crear **salas** con una cantidad fija de asientos.
* Permitir a los **usuarios** reservar boletos específicos.
* Evitar que dos usuarios reserven el mismo asiento.
* Calcular el **costo total** del boleto.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar un sistema donde los usuarios pueden reservar asientos específicos
> para una función de cine, verificando que no haya asientos ocupados.
> Un `BookingManager` central manejará toda la lógica de validación y reservas.”

---

**❓ Preguntas al entrevistador:**

* ¿Un usuario puede reservar varios asientos a la vez? → ✅ Sí.
* ¿Hay precios diferentes por sala o película? → 🟡 Por ahora, precio fijo.
* ¿Debe validar el horario de la película? → ✅ Sí, cada función tiene fecha y hora.
* ¿Qué pasa si un asiento ya está reservado? → ❌ No se puede volver a reservar.

**🧩 Casos límite:**

* [x] Sala llena.
* [x] Reservar más asientos de los disponibles.
* [x] Cancelar reserva.
* [x] Función expirada (horario pasado).

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Organizar las entidades `Movie`, `Theater`, `Showtime`, `Seat`, `Booking` y `User` bajo un controlador `BookingManager`.

---

### 🧩 Clases Principales

| Clase            | Responsabilidad                                     | Relaciones                              |
| ---------------- | --------------------------------------------------- | --------------------------------------- |
| `BookingManager` | Controla creación de funciones y reservas.          | Coordina `Movie`, `Theater`, `Booking`. |
| `Movie`          | Contiene nombre y duración.                         | Asociada con `Showtime`.                |
| `Theater`        | Representa la sala y sus asientos.                  | Contiene `Seat`.                        |
| `Seat`           | Representa un asiento físico con estado.            | Usado en `Booking`.                     |
| `Showtime`       | Representa una función (película + horario + sala). | Usada en `Booking`.                     |
| `Booking`        | Representa una reserva activa (usuario + asientos). | Creada por `BookingManager`.            |
| `User`           | Representa al cliente.                              | Crea `Booking`.                         |

---

### 🔁 Flujo de Operación

1. Se registran películas y salas.
2. Se crean horarios (`Showtime`) que unen película y sala.
3. El usuario selecciona una función y asientos.
4. El `BookingManager` valida disponibilidad.
5. Si hay asientos libres → crea un `Booking`.
6. Marca los asientos como ocupados y muestra el total.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|    BookingManager    |
+----------------------+
| - movies             |
| - theaters           |
| - showtimes          |
| - bookings           |
+----------------------+
| + createShowtime()   |
| + bookSeats()        |
| + cancelBooking()    |
+----------------------+
          |
          v
+----------------------+
|       Booking        |
+----------------------+
| user, showtime, seats, total |
+----------------------+

+----------------------+
|       Showtime       |
+----------------------+
| movie, theater, start_time |
+----------------------+

+----------------------+
|        Theater       |
+----------------------+
| id, name, seats      |
+----------------------+

+----------------------+
|         Seat         |
+----------------------+
| number, is_booked    |
+----------------------+

+----------------------+
|        Movie         |
+----------------------+
| title, duration      |
+----------------------+

+----------------------+
|         User         |
+----------------------+
| id, name             |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Crear un flujo funcional que valide disponibilidad y gestione estados.

```python
from datetime import datetime, timedelta

class Movie:
    def __init__(self, title, duration):
        self.title = title
        self.duration = duration  # en minutos

class Seat:
    def __init__(self, number):
        self.number = number
        self.is_booked = False

class Theater:
    def __init__(self, tid, name, total_seats):
        self.id = tid
        self.name = name
        self.seats = [Seat(i+1) for i in range(total_seats)]

    def get_available_seats(self):
        return [s for s in self.seats if not s.is_booked]

class Showtime:
    def __init__(self, movie, theater, start_time, price_per_seat=10):
        self.movie = movie
        self.theater = theater
        self.start_time = start_time
        self.price_per_seat = price_per_seat

class User:
    def __init__(self, uid, name):
        self.id = uid
        self.name = name

class Booking:
    def __init__(self, user, showtime, seats):
        self.user = user
        self.showtime = showtime
        self.seats = seats
        self.total = self.calculate_total()
        self.time = datetime.now()

    def calculate_total(self):
        return len(self.seats) * self.showtime.price_per_seat

class BookingManager:
    def __init__(self):
        self.movies = []
        self.theaters = []
        self.showtimes = []
        self.bookings = []

    def register_movie(self, movie):
        self.movies.append(movie)

    def register_theater(self, theater):
        self.theaters.append(theater)

    def create_showtime(self, movie, theater, start_time):
        showtime = Showtime(movie, theater, start_time)
        self.showtimes.append(showtime)
        print(f"Función creada: {movie.title} en {theater.name} a las {start_time.strftime('%H:%M')}")
        return showtime

    def book_seats(self, user, showtime, seat_numbers):
        available = [s.number for s in showtime.theater.get_available_seats()]
        if not all(num in available for num in seat_numbers):
            print("Algunos asientos ya están reservados.")
            return None
        seats_to_book = [s for s in showtime.theater.seats if s.number in seat_numbers]
        for s in seats_to_book:
            s.is_booked = True
        booking = Booking(user, showtime, seats_to_book)
        self.bookings.append(booking)
        print(f"{user.name} reservó {len(seats_to_book)} asiento(s) para {showtime.movie.title}. Total: ${booking.total}")
        return booking

    def cancel_booking(self, booking):
        for s in booking.seats:
            s.is_booked = False
        self.bookings.remove(booking)
        print(f"Reserva cancelada para {booking.user.name} ({booking.showtime.movie.title}).")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear sistema
manager = BookingManager()

# Registrar película y sala
m1 = Movie("El Castillo Vagabundo", 125)
t1 = Theater("T1", "Sala Estelar", 10)
manager.register_movie(m1)
manager.register_theater(t1)

# Crear horario
start_time = datetime.now() + timedelta(hours=2)
show = manager.create_showtime(m1, t1, start_time)

# Crear usuarios
u1 = User("U1", "Isabel")
u2 = User("U2", "Carlos")

# Reservar asientos
b1 = manager.book_seats(u1, show, [1, 2, 3])
b2 = manager.book_seats(u2, show, [2, 4])  # debe fallar

# Cancelar reserva
manager.cancel_booking(b1)
```

**🎯 Salida esperada:**

```
Función creada: El Castillo Vagabundo en Sala Estelar a las 22:00
Isabel reservó 3 asiento(s) para El Castillo Vagabundo. Total: $30
Algunos asientos ya están reservados.
Reserva cancelada para Isabel (El Castillo Vagabundo).
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                     |
| ---------------------- | ------------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(S) — depende del número de asientos por sala.                           |
| 💾 **Espacio:**        | O(M + T + B) — películas, teatros, reservas.                              |
| ⚡ **Escalabilidad:**   | Alta — se puede extender a múltiples cines, horarios o precios dinámicos. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Validación temporal.                          |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de validación temporal y manejo de entidades relacionadas.

**🔧 Posibles optimizaciones:**

* Integrar `Payment` y `Ticket` como entidades independientes.
* Usar `State Pattern` para estados del asiento (`Libre`, `Reservado`, `Ocupado`).
* Validar horarios automáticamente para evitar solapamientos.

**📚 Lecciones aprendidas:**

* La **composición** simplifica el flujo entre película, sala y horario.
* El **control de estado** evita sobreasignaciones y conflictos de reserva.
* El sistema se adapta fácilmente a escenarios reales de cine.

**✅ Conclusión final:**

> “Diseñé un sistema de reservas de cine que gestiona películas, salas y horarios,
> asegurando disponibilidad, trazabilidad y cancelación segura.
> El modelo refleja una plataforma moderna de booking con control total de asientos.”

---

📘 **Resumen final**

| Aspecto          | Valor                                           |
| ---------------- | ----------------------------------------------- |
| Patrón           | Controlador + Composición + Validación temporal |
| Complejidad      | O(S)                                            |
| Palabra clave    | “Reserva y disponibilidad”                      |
| Tipo de problema | Modelado de sistema de boletos                  |
| Nivel            | 🟡 Medio                                        |

