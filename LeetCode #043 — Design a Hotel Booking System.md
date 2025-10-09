# 🏨 **LeetCode #043 — Design a Hotel Booking System**

> **Tema:** Reservas de habitaciones, disponibilidad, pagos y cancelaciones
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Validación temporal y de disponibilidad*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que modele un **sistema de reservaciones hoteleras**,
> con control de **clientes, habitaciones, fechas, pagos y cancelaciones**,
> aplicando validaciones de disponibilidad y registro histórico.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **clientes**.
* Administrar **habitaciones** (número, tipo, precio, estado).
* Crear y cancelar **reservas** en fechas específicas.
* Calcular el **costo total** según las noches.
* Registrar **pagos** y emitir comprobantes.
* Evitar **solapamiento de fechas** para la misma habitación.

---

**💬 Reexplicación en voz alta:**

> “Voy a construir un sistema de reservas hoteleras donde los clientes pueden reservar habitaciones en fechas específicas.
> Cada habitación tiene tipo, precio y disponibilidad controlada.
> El `HotelManager` centraliza las reservas, validaciones y pagos.”

---

**❓ Preguntas al entrevistador:**

* ¿Se permiten múltiples reservas por cliente? → ✅ Sí.
* ¿Puede haber diferentes tipos de habitaciones? → ✅ Sí (estándar, suite, etc.).
* ¿Debe incluir cancelaciones y reembolsos? → ✅ Sí, cancelación básica.
* ¿Las fechas de reserva pueden traslaparse? → ❌ No, deben validarse.

**🧩 Casos límite:**

* [x] Habitaciones agotadas.
* [x] Fechas invertidas (check-out antes de check-in).
* [x] Reserva duplicada en las mismas fechas.
* [x] Cancelación de reserva inexistente.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Organizar las entidades principales — `Room`, `Customer`, `Reservation`, `Payment` —
> con control de disponibilidad por fecha y cálculo automático del costo total.

---

### 🧩 Clases Principales

| Clase          | Responsabilidad                                      | Relaciones                           |
| -------------- | ---------------------------------------------------- | ------------------------------------ |
| `HotelManager` | Coordina clientes, habitaciones y reservas.          | Control central del sistema.         |
| `Customer`     | Representa al huésped.                               | Puede tener múltiples `Reservation`. |
| `Room`         | Representa una habitación con tipo, precio y estado. | Asociada a `Reservation`.            |
| `Reservation`  | Representa la reserva de una habitación.             | Incluye fechas, cliente y pago.      |
| `Payment`      | Gestiona el pago de una reserva.                     | Asociada a `Reservation`.            |

---

### 🔁 Flujo de Operación

1. Registrar cliente y habitaciones.
2. Consultar disponibilidad por rango de fechas.
3. Crear reserva (check-in / check-out).
4. Calcular costo total y registrar pago.
5. Permitir cancelaciones (con reembolso parcial o total).

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|    HotelManager      |
+----------------------+
| - rooms, customers, reservations |
+----------------------+
| + registerCustomer() |
| + addRoom()          |
| + checkAvailability()|
| + makeReservation()  |
| + cancelReservation()|
+----------------------+
          |
          v
+----------------------+
|     Reservation      |
+----------------------+
| customer, room, check_in, check_out, total, payment |
+----------------------+

+----------------------+
|        Room          |
+----------------------+
| number, type, price, reservations |
+----------------------+

+----------------------+
|      Customer        |
+----------------------+
| id, name, reservations |
+----------------------+

+----------------------+
|       Payment        |
+----------------------+
| amount, status, date |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar flujo completo de reserva, validación de disponibilidad y cancelación.

```python
from datetime import datetime

class PaymentStatus:
    PENDING = "Pendiente"
    COMPLETED = "Pagado"
    REFUNDED = "Reembolsado"

class Payment:
    def __init__(self, amount):
        self.amount = amount
        self.status = PaymentStatus.PENDING
        self.date = None

    def process(self):
        self.status = PaymentStatus.COMPLETED
        self.date = datetime.now()
        print(f"💳 Pago procesado por ${self.amount:.2f}")

    def refund(self):
        if self.status != PaymentStatus.COMPLETED:
            print("⚠️ No se puede reembolsar un pago pendiente.")
            return
        self.status = PaymentStatus.REFUNDED
        print(f"💸 Reembolso realizado de ${self.amount:.2f}")

class Room:
    def __init__(self, number, room_type, price):
        self.number = number
        self.type = room_type
        self.price = price
        self.reservations = []

    def is_available(self, check_in, check_out):
        for r in self.reservations:
            if not (check_out <= r.check_in or check_in >= r.check_out):
                return False
        return True

    def __str__(self):
        return f"Habitación {self.number} ({self.type}) - ${self.price:.2f}/noche"

class Customer:
    def __init__(self, cid, name):
        self.id = cid
        self.name = name
        self.reservations = []

class Reservation:
    def __init__(self, customer, room, check_in, check_out):
        self.customer = customer
        self.room = room
        self.check_in = check_in
        self.check_out = check_out
        self.nights = (check_out - check_in).days
        self.total = self.nights * room.price
        self.payment = Payment(self.total)
        self.status = "Activa"

    def confirm(self):
        self.room.reservations.append(self)
        self.customer.reservations.append(self)
        self.payment.process()
        print(f"✅ Reserva confirmada para {self.customer.name} en {self.room}")
        print(f"📅 Estancia: {self.nights} noche(s) | Total: ${self.total:.2f}")

    def cancel(self):
        if self.status != "Activa":
            print("⚠️ Reserva ya cancelada.")
            return
        self.status = "Cancelada"
        self.room.reservations.remove(self)
        self.payment.refund()
        print(f"❌ Reserva cancelada para {self.customer.name} en {self.room}")

class HotelManager:
    def __init__(self):
        self.rooms = []
        self.customers = {}
        self.reservations = []

    def register_customer(self, cid, name):
        if cid in self.customers:
            print("⚠️ Cliente ya registrado.")
            return
        c = Customer(cid, name)
        self.customers[cid] = c
        print(f"🧍 Cliente registrado: {name}")
        return c

    def add_room(self, number, room_type, price):
        r = Room(number, room_type, price)
        self.rooms.append(r)
        print(f"🏠 {r}")
        return r

    def check_availability(self, room_type, check_in, check_out):
        available = [r for r in self.rooms if r.type == room_type and r.is_available(check_in, check_out)]
        print(f"🔎 Habitaciones disponibles ({room_type}): {len(available)}")
        for r in available:
            print(f"  - {r}")
        return available

    def make_reservation(self, cid, room_type, check_in, check_out):
        customer = self.customers.get(cid)
        if not customer:
            print("🚫 Cliente no encontrado.")
            return
        if check_in >= check_out:
            print("⚠️ Fechas inválidas.")
            return
        available_rooms = self.check_availability(room_type, check_in, check_out)
        if not available_rooms:
            print("🚫 No hay habitaciones disponibles.")
            return
        room = available_rooms[0]
        res = Reservation(customer, room, check_in, check_out)
        res.confirm()
        self.reservations.append(res)
        return res

    def cancel_reservation(self, reservation):
        reservation.cancel()
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear hotel
hotel = HotelManager()

# Registrar cliente y habitaciones
u1 = hotel.register_customer("C1", "Isabel")
r1 = hotel.add_room(101, "Suite", 200)
r2 = hotel.add_room(102, "Suite", 200)
r3 = hotel.add_room(201, "Estándar", 100)

# Fechas
check_in = datetime(2025, 10, 10)
check_out = datetime(2025, 10, 13)

# Crear reservas
res1 = hotel.make_reservation("C1", "Suite", check_in, check_out)
res2 = hotel.make_reservation("C1", "Suite", check_in, check_out)  # segunda suite
hotel.cancel_reservation(res1)
```

**🎯 Salida esperada:**

```
🧍 Cliente registrado: Isabel
🏠 Habitación 101 (Suite) - $200.00/noche
🏠 Habitación 102 (Suite) - $200.00/noche
🏠 Habitación 201 (Estándar) - $100.00/noche
🔎 Habitaciones disponibles (Suite): 2
  - Habitación 101 (Suite) - $200.00/noche
  - Habitación 102 (Suite) - $200.00/noche
💳 Pago procesado por $600.00
✅ Reserva confirmada para Isabel en Habitación 101 (Suite) - $200.00/noche
📅 Estancia: 3 noche(s) | Total: $600.00
🔎 Habitaciones disponibles (Suite): 1
  - Habitación 102 (Suite) - $200.00/noche
💳 Pago procesado por $600.00
✅ Reserva confirmada para Isabel en Habitación 102 (Suite) - $200.00/noche
📅 Estancia: 3 noche(s) | Total: $600.00
💸 Reembolso realizado de $600.00
❌ Reserva cancelada para Isabel en Habitación 101 (Suite) - $200.00/noche
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                |
| ---------------------- | -------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(R) — búsqueda de disponibilidad.                                   |
| 💾 **Espacio:**        | O(C + R + B) — clientes, reservas y habitaciones.                    |
| ⚡ **Escalabilidad:**   | Alta — se puede integrar con APIs de pago y sincronización en línea. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Validación temporal.                     |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de validación temporal, cancelaciones y control de disponibilidad.

**🔧 Posibles optimizaciones:**

* Añadir `BookingCalendar` para control visual de fechas.
* Implementar `Discount` o `Coupon` para ofertas.
* Integrar `PaymentGateway` (Stripe, PayPal, etc.).

**📚 Lecciones aprendidas:**

* La **validación de solapamientos** es esencial en sistemas de reservas.
* El flujo *reservar → pagar → cancelar* refleja procesos reales de negocio.
* El modelo puede escalar fácilmente a sistemas distribuidos o multi-hotel.

**✅ Conclusión final:**

> “Diseñé un sistema hotelero con reservas, pagos y cancelaciones seguras,
> aplicando validaciones temporales y control de disponibilidad.
> El diseño refleja el comportamiento real de sistemas como Booking o Expedia.”

---

📘 **Resumen final**

| Aspecto          | Valor                                           |
| ---------------- | ----------------------------------------------- |
| Patrón           | Controlador + Composición + Validación temporal |
| Complejidad      | O(R)                                            |
| Palabra clave    | “Reservas seguras con disponibilidad dinámica”  |
| Tipo de problema | Modelado de sistema hotelero                    |
| Nivel            | 🟡 Medio                                        |

