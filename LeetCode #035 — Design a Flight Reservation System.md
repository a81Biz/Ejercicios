# ✈️ **LeetCode #035 — Design a Flight Reservation System**

> **Tema:** Gestión de reservas jerárquicas con control temporal
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Validación de disponibilidad*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que permita **reservar vuelos**,
> administrando **pasajeros, vuelos, asientos, fechas y disponibilidad**,
> con control de duplicados y asignación de asientos únicos.

---

**📖 Enunciado resumido:**
El sistema debe permitir:

* Registrar **vuelos** con fecha, hora y capacidad.
* Registrar **pasajeros**.
* Crear **reservas** (un pasajero ↔ un vuelo ↔ asiento único).
* Impedir duplicar reservas para el mismo pasajero y vuelo.
* Mostrar disponibilidad y lista de pasajeros por vuelo.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar un sistema de reservas aéreas donde cada vuelo tiene asientos únicos.
> Los pasajeros pueden reservar un asiento específico en un vuelo,
> y el controlador central (`FlightManager`) validará la disponibilidad antes de confirmar.”

---

**❓ Preguntas al entrevistador:**

* ¿Un pasajero puede reservar múltiples vuelos? → ✅ Sí, distintos vuelos.
* ¿Se permite elegir asiento específico? → ✅ Sí.
* ¿Debe manejarse fecha y hora reales? → ✅ Sí.
* ¿Se permiten cancelaciones? → ✅ Sí.

**🧩 Casos límite:**

* [x] Vuelo lleno.
* [x] Pasajero reserva el mismo vuelo dos veces.
* [x] Asiento ocupado.
* [x] Reserva en vuelo inexistente.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Estructurar las entidades en jerarquía clara:
> `FlightManager` → `Flight` → `Seat` y `Reservation`, vinculadas con `Passenger`.

---

### 🧩 Clases Principales

| Clase           | Responsabilidad                                              | Relaciones                                     |
| --------------- | ------------------------------------------------------------ | ---------------------------------------------- |
| `FlightManager` | Controla las operaciones de registro, reserva y cancelación. | Coordina `Flight`, `Passenger`, `Reservation`. |
| `Flight`        | Representa un vuelo con fecha, hora y asientos.              | Contiene `Seat`.                               |
| `Seat`          | Representa un asiento individual (ocupado o libre).          | Usado en `Reservation`.                        |
| `Passenger`     | Representa a un cliente con ID y nombre.                     | Puede tener múltiples reservas.                |
| `Reservation`   | Asocia un pasajero con un asiento y vuelo.                   | Creada por `FlightManager`.                    |

---

### 🔁 Flujo de Operación

1. Se registran vuelos con fecha, hora y capacidad.
2. Los pasajeros se registran en el sistema.
3. Un pasajero solicita un asiento en un vuelo.
4. Si está disponible, se crea una reserva (`Reservation`).
5. Si no, el sistema muestra un error.
6. Se pueden consultar reservas o cancelarlas.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|    FlightManager     |
+----------------------+
| - flights            |
| - passengers         |
| - reservations       |
+----------------------+
| + addFlight()        |
| + registerPassenger()|
| + reserveSeat()      |
| + cancelReservation()|
| + showAvailability() |
+----------------------+
          |
          v
+----------------------+
|      Flight          |
+----------------------+
| flight_no, date, seats |
+----------------------+
| + getAvailableSeats() |
| + getSeat()           |
+----------------------+

+----------------------+
|        Seat          |
+----------------------+
| number, occupied     |
+----------------------+

+----------------------+
|      Passenger       |
+----------------------+
| id, name             |
+----------------------+

+----------------------+
|     Reservation      |
+----------------------+
| passenger, flight, seat |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Controlar la disponibilidad de asientos y gestionar reservas únicas.

```python
from datetime import datetime, timedelta

class Seat:
    def __init__(self, number):
        self.number = number
        self.occupied = False

class Flight:
    def __init__(self, flight_no, date, capacity):
        self.flight_no = flight_no
        self.date = date
        self.seats = [Seat(i+1) for i in range(capacity)]

    def get_available_seats(self):
        return [s for s in self.seats if not s.occupied]

    def get_seat(self, seat_number):
        for seat in self.seats:
            if seat.number == seat_number:
                return seat
        return None

class Passenger:
    def __init__(self, pid, name):
        self.id = pid
        self.name = name

class Reservation:
    def __init__(self, passenger, flight, seat):
        self.passenger = passenger
        self.flight = flight
        self.seat = seat
        self.time = datetime.now()

class FlightManager:
    def __init__(self):
        self.flights = []
        self.passengers = []
        self.reservations = []

    def add_flight(self, flight_no, date, capacity):
        flight = Flight(flight_no, date, capacity)
        self.flights.append(flight)
        print(f"✈️ Vuelo {flight_no} creado con {capacity} asientos.")
        return flight

    def register_passenger(self, pid, name):
        p = Passenger(pid, name)
        self.passengers.append(p)
        print(f"🧍‍♀️ Pasajero {name} registrado.")
        return p

    def find_flight(self, flight_no):
        for f in self.flights:
            if f.flight_no == flight_no:
                return f
        return None

    def find_reservation(self, passenger, flight):
        for r in self.reservations:
            if r.passenger == passenger and r.flight == flight:
                return r
        return None

    def reserve_seat(self, passenger, flight, seat_number):
        # Verificar vuelo existente
        if not flight:
            print("🚫 Vuelo no encontrado.")
            return

        # Verificar duplicado
        if self.find_reservation(passenger, flight):
            print(f"⚠️ {passenger.name} ya tiene una reserva en vuelo {flight.flight_no}.")
            return

        seat = flight.get_seat(seat_number)
        if not seat or seat.occupied:
            print(f"🚫 Asiento {seat_number} no disponible.")
            return

        seat.occupied = True
        reservation = Reservation(passenger, flight, seat)
        self.reservations.append(reservation)
        print(f"✅ {passenger.name} reservó asiento {seat_number} en vuelo {flight.flight_no}.")

    def cancel_reservation(self, passenger, flight):
        r = self.find_reservation(passenger, flight)
        if not r:
            print("⚠️ No se encontró reserva para cancelar.")
            return
        r.seat.occupied = False
        self.reservations.remove(r)
        print(f"❌ Reserva de {passenger.name} en vuelo {flight.flight_no} cancelada.")

    def show_availability(self, flight):
        available = len(flight.get_available_seats())
        print(f"🪑 Disponibles en vuelo {flight.flight_no}: {available}/{len(flight.seats)}")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear sistema
manager = FlightManager()

# Registrar vuelo y pasajeros
flight = manager.add_flight("CV001", datetime.now() + timedelta(days=1), capacity=5)
p1 = manager.register_passenger("P1", "Isabel")
p2 = manager.register_passenger("P2", "Carlos")

# Reservas
manager.reserve_seat(p1, flight, 1)
manager.reserve_seat(p2, flight, 1)  # debe fallar
manager.reserve_seat(p2, flight, 2)
manager.show_availability(flight)

# Cancelar
manager.cancel_reservation(p1, flight)
manager.show_availability(flight)
```

**🎯 Salida esperada:**

```
✈️ Vuelo CV001 creado con 5 asientos.
🧍‍♀️ Pasajero Isabel registrado.
🧍‍♀️ Pasajero Carlos registrado.
✅ Isabel reservó asiento 1 en vuelo CV001.
🚫 Asiento 1 no disponible.
✅ Carlos reservó asiento 2 en vuelo CV001.
🪑 Disponibles en vuelo CV001: 3/5
❌ Reserva de Isabel en vuelo CV001 cancelada.
🪑 Disponibles en vuelo CV001: 4/5
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                          |
| ---------------------- | -------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(F + S) — búsqueda de vuelo y asiento.                        |
| 💾 **Espacio:**        | O(P + R) — pasajeros y reservas activas.                       |
| ⚡ **Escalabilidad:**   | Alta — se pueden añadir rutas, aerolíneas o clases de asiento. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Validación.                        |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de control de disponibilidad y gestión de entidades jerárquicas.

**🔧 Posibles optimizaciones:**

* Integrar `FlightSchedule` para vuelos recurrentes.
* Agregar `SeatClass` (económica, ejecutiva, primera).
* Implementar `Payment` y `Ticket` electrónicos.

**📚 Lecciones aprendidas:**

* La **composición jerárquica** (Vuelo → Asientos → Reservas) simplifica validaciones.
* El control de duplicados evita inconsistencias en sistemas multiusuario.
* La arquitectura es extensible para aerolíneas completas o múltiples rutas.

**✅ Conclusión final:**

> “Diseñé un sistema de reservas de vuelo que maneja pasajeros, vuelos y asientos
> con control de disponibilidad, cancelación y validación de duplicados.
> El diseño refleja la estructura lógica de un sistema de aerolínea real.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | Controlador + Composición + Validación |
| Complejidad      | O(F + S)                               |
| Palabra clave    | “Disponibilidad jerárquica”            |
| Tipo de problema | Modelado de reservas aéreas            |
| Nivel            | 🟡 Medio                               |

---