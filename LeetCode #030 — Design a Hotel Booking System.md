# 🏨 **LeetCode #030 — Design a Hotel Booking System**

> **Tema:** Modelado de reservas, disponibilidad y gestión temporal
> **Nivel:** 🟡 Medio
> **Patrón:** *Entidad-Controlador + Estado + Relaciones cruzadas*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que administre **reservas de hotel**,
> controlando habitaciones, huéspedes, fechas y estados de ocupación.

---

**📖 Enunciado resumido:**
El sistema debe permitir:

* Registrar **habitaciones** (con tipos y tarifas).
* Registrar **clientes** (huéspedes).
* Crear **reservas** con fechas de entrada y salida.
* Verificar disponibilidad antes de confirmar.
* Actualizar estados (`Disponible`, `Reservada`, `Ocupada`).

---

**💬 Reexplicación en voz alta:**

> “Necesito representar un hotel como una colección de habitaciones,
> clientes que pueden reservarlas, y un controlador que asegure la disponibilidad
> y gestione el estado de cada habitación a lo largo del tiempo.”

---

**❓ Preguntas al entrevistador:**

* ¿Se deben manejar diferentes tipos de habitación (Suite, Doble, Individual)? → ✅ Sí.
* ¿Debe permitir múltiples reservas futuras de la misma habitación? → ✅ Sí, si no hay solapamiento.
* ¿Qué pasa si dos reservas se cruzan en fechas? → ❌ No se permite.
* ¿Debe calcular el costo total de la estancia? → ✅ Sí, basado en tarifa y duración.

**🧩 Casos límite:**

* [x] Reserva que se solapa con otra existente.
* [x] Check-in sin reserva.
* [x] Check-out antes de la fecha.
* [x] Cancelación de reserva.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Representar el sistema con clases que reflejen los roles del dominio (Hotel, Room, Booking, Guest).

---

### 🧩 Clases Principales

| Clase     | Responsabilidad                                             | Relaciones                           |
| --------- | ----------------------------------------------------------- | ------------------------------------ |
| `Hotel`   | Controla el inventario y las reservas.                      | Contiene `Room`, `Booking`, `Guest`. |
| `Room`    | Representa una habitación física con tipo, tarifa y estado. | Usada por `Booking`.                 |
| `Guest`   | Representa un cliente del hotel.                            | Posee múltiples `Booking`.           |
| `Booking` | Representa una reserva (cliente + habitación + fechas).     | Une `Room` y `Guest`.                |

---

### 🔁 Flujo de Operación

1. Cliente se registra (`Guest`).
2. Solicita una habitación con fechas.
3. El sistema verifica disponibilidad (`Hotel.check_availability`).
4. Si está libre → crea una `Booking` y marca la habitación como reservada.
5. Al hacer check-in → cambia a `Ocupada`.
6. Al hacer check-out → vuelve a `Disponible`.

---

### 🧱 Relaciones UML (simplificadas)

```
+-------------------+
|      Hotel        |
+-------------------+
| - rooms           |
| - bookings        |
| - guests          |
+-------------------+
| + addRoom()       |
| + registerGuest() |
| + createBooking() |
| + checkAvailability() |
+-------------------+
          |
          v
+-------------------+
|      Room         |
+-------------------+
| number, type, rate, status |
+-------------------+
| + isAvailable()   |
| + setStatus()     |
+-------------------+

+-------------------+
|      Guest        |
+-------------------+
| id, name          |
+-------------------+

+-------------------+
|     Booking       |
+-------------------+
| guest, room, dates, total |
+-------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Construir un modelo legible y funcional, con estados claros y control temporal.

```python
from datetime import datetime, timedelta
from enum import Enum

class RoomStatus(Enum):
    AVAILABLE = "Disponible"
    RESERVED = "Reservada"
    OCCUPIED = "Ocupada"

class Room:
    def __init__(self, number, room_type, rate):
        self.number = number
        self.room_type = room_type
        self.rate = rate
        self.status = RoomStatus.AVAILABLE

    def is_available(self):
        return self.status == RoomStatus.AVAILABLE

    def set_status(self, new_status):
        self.status = new_status


class Guest:
    def __init__(self, guest_id, name):
        self.guest_id = guest_id
        self.name = name


class Booking:
    def __init__(self, guest, room, start_date, end_date):
        self.guest = guest
        self.room = room
        self.start_date = start_date
        self.end_date = end_date
        self.total_cost = self.calculate_cost()
        self.active = True

    def calculate_cost(self):
        nights = (self.end_date - self.start_date).days
        return nights * self.room.rate

    def overlaps(self, other_booking):
        return self.room == other_booking.room and not (
            self.end_date <= other_booking.start_date or self.start_date >= other_booking.end_date
        )

    def cancel(self):
        self.active = False


class Hotel:
    def __init__(self, name):
        self.name = name
        self.rooms = []
        self.guests = []
        self.bookings = []

    def add_room(self, room):
        self.rooms.append(room)

    def register_guest(self, guest):
        self.guests.append(guest)

    def check_availability(self, room, start_date, end_date):
        for booking in self.bookings:
            if booking.room == room and booking.active and booking.overlaps(Booking(None, room, start_date, end_date)):
                return False
        return True

    def create_booking(self, guest, room, start_date, end_date):
        if not self.check_availability(room, start_date, end_date):
            print(f"La habitación {room.number} no está disponible en esas fechas.")
            return None
        booking = Booking(guest, room, start_date, end_date)
        self.bookings.append(booking)
        room.set_status(RoomStatus.RESERVED)
        print(f"Reserva creada: {guest.name} → Habitación {room.number} del {start_date.date()} al {end_date.date()}")
        return booking

    def check_in(self, booking):
        booking.room.set_status(RoomStatus.OCCUPIED)
        print(f"Check-in completado para {booking.guest.name} en habitación {booking.room.number}")

    def check_out(self, booking):
        booking.room.set_status(RoomStatus.AVAILABLE)
        booking.active = False
        print(f"Check-out completado. Total: ${booking.total_cost}")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear hotel y registros
hotel = Hotel("Castillo Vagabundo Inn")

hotel.add_room(Room(101, "Suite", 150))
hotel.add_room(Room(102, "Doble", 100))
hotel.add_room(Room(103, "Individual", 80))

guest1 = Guest("G1", "Isabel")
guest2 = Guest("G2", "Carlos")

hotel.register_guest(guest1)
hotel.register_guest(guest2)

# Fechas
today = datetime.now()
tomorrow = today + timedelta(days=1)
next_week = today + timedelta(days=7)

# Crear reservas
booking1 = hotel.create_booking(guest1, hotel.rooms[0], today, next_week)
booking2 = hotel.create_booking(guest2, hotel.rooms[0], today, next_week)  # Debe fallar

# Check-in y check-out
if booking1:
    hotel.check_in(booking1)
    hotel.check_out(booking1)
```

**🎯 Salida esperada:**

```
Reserva creada: Isabel → Habitación 101 del 2025-10-08 al 2025-10-15
La habitación 101 no está disponible en esas fechas.
Check-in completado para Isabel en habitación 101
Check-out completado. Total: $1050
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                  |
| ---------------------- | ---------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) para validar disponibilidad (N = número de reservas activas).     |
| 💾 **Espacio:**        | O(R + G + H) — reservas, huéspedes y habitaciones.                     |
| ⚡ **Escalabilidad:**   | Alta — se pueden añadir sucursales, métodos de pago o integración web. |
| 🧩 **Tipo de patrón:** | Controlador + Entidades con estado.                                    |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de modelado temporal, estados y relaciones cruzadas.

**🔧 Posibles optimizaciones:**

* Integrar `Payment` y `Invoice`.
* Control de estados con `State Pattern`.
* Validación de solapamiento mediante índices temporales.

**📚 Lecciones aprendidas:**

* Los **estados** (`Disponible`, `Reservada`, `Ocupada`) son clave para reflejar el mundo real.
* La **composición** (`Booking` une `Room` y `Guest`) simplifica la trazabilidad.
* El diseño puede extenderse fácilmente a sistemas distribuidos o multi-hotel.

**✅ Conclusión final:**

> “Modelé un sistema de reservas de hotel con control de disponibilidad, fechas y tarifas.
> El diseño aplica encapsulación, composición y gestión de estado,
> reflejando un flujo real de check-in / check-out de manera estructurada.”

---

📘 **Resumen final**

| Aspecto          | Valor                               |
| ---------------- | ----------------------------------- |
| Patrón           | Controlador + Estado + Composición  |
| Complejidad      | O(N)                                |
| Palabra clave    | “Gestión temporal y disponibilidad” |
| Tipo de problema | Modelado de sistema de reservas     |
| Nivel            | 🟡 Medio                            |

---
