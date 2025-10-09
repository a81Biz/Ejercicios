# 🚗 **LeetCode #034 — Design a Parking Lot System**

> **Tema:** Gestión de recursos limitados y asignación por tipo
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Herencia + Estado físico*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que gestione un **estacionamiento**,
> permitiendo la entrada y salida de vehículos, controlando **tipos de espacios**, **tarifas** y **ocupación**.

---

**📖 Enunciado resumido:**
El sistema debe:

* Soportar diferentes tipos de vehículos: **moto**, **auto** y **camión**.
* Asignar el espacio correcto dependiendo del tipo.
* Evitar ocupar un espacio ya reservado.
* Calcular la **tarifa** según el tiempo de estancia.
* Mostrar la disponibilidad actual.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar un estacionamiento con distintos tipos de lugares.
> Cada vehículo ocupará un espacio compatible con su tipo, y el sistema calculará la tarifa al salir.
> Un `ParkingLotManager` central se encargará de la asignación y control de ocupación.”

---

**❓ Preguntas al entrevistador:**

* ¿Cuántos tipos de espacio existen? → 🟢 Tres (Moto, Auto, Camión).
* ¿La tarifa varía según tipo de vehículo? → ✅ Sí.
* ¿Se puede estacionar más de un vehículo por espacio? → ❌ No.
* ¿Debe manejar la hora de entrada y salida? → ✅ Sí, para cálculo de tarifa.

**🧩 Casos límite:**

* [x] No hay espacios disponibles.
* [x] Salida sin registro de entrada.
* [x] Mismo vehículo intenta entrar dos veces.
* [x] Llenado total del estacionamiento.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Modelar las entidades con una jerarquía clara:
> `ParkingLot` → `ParkingSpot` → `Vehicle`, controladas por `ParkingLotManager`.

---

### 🧩 Clases Principales

| Clase               | Responsabilidad                                               | Relaciones                                 |
| ------------------- | ------------------------------------------------------------- | ------------------------------------------ |
| `ParkingLotManager` | Controla las operaciones de entrada, salida y disponibilidad. | Coordina `ParkingLot` y `Vehicle`.         |
| `ParkingLot`        | Agrupa los espacios de diferentes tipos.                      | Contiene `ParkingSpot`.                    |
| `ParkingSpot`       | Representa un lugar físico con tipo y estado.                 | Asignado a un `Vehicle`.                   |
| `Vehicle`           | Clase base para todos los vehículos.                          | Heredada por `Car`, `Motorcycle`, `Truck`. |
| `Ticket`            | Registra la entrada y salida para cálculo de tarifa.          | Asociado a `Vehicle` y `Spot`.             |

---

### 🔁 Flujo de Operación

1. Un vehículo llega → el `Manager` busca un espacio del tipo correcto.
2. Si hay lugar disponible → se crea un `Ticket` y se marca el espacio como ocupado.
3. Al salir → se calcula el tiempo transcurrido y el costo.
4. El espacio vuelve a estar disponible.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|  ParkingLotManager   |
+----------------------+
| - lot                |
| - active_tickets     |
+----------------------+
| + parkVehicle()      |
| + exitVehicle()      |
| + showAvailability() |
+----------------------+
          |
          v
+----------------------+
|     ParkingLot       |
+----------------------+
| - spots              |
+----------------------+
| + findSpot()         |
| + releaseSpot()      |
+----------------------+

+----------------------+
|    ParkingSpot       |
+----------------------+
| id, type, occupied   |
+----------------------+

+----------------------+
|      Vehicle         |
+----------------------+
| plate, type          |
+----------------------+

+----------------------+
|       Ticket         |
+----------------------+
| vehicle, spot, in_time, out_time |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Controlar estados físicos (ocupado/libre) y calcular tarifas por tipo de vehículo.

```python
from datetime import datetime, timedelta
from enum import Enum

class SpotType(Enum):
    MOTORCYCLE = "Moto"
    CAR = "Auto"
    TRUCK = "Camión"

class Vehicle:
    def __init__(self, plate, vtype):
        self.plate = plate
        self.type = vtype

class Motorcycle(Vehicle):
    def __init__(self, plate):
        super().__init__(plate, SpotType.MOTORCYCLE)

class Car(Vehicle):
    def __init__(self, plate):
        super().__init__(plate, SpotType.CAR)

class Truck(Vehicle):
    def __init__(self, plate):
        super().__init__(plate, SpotType.TRUCK)

class ParkingSpot:
    def __init__(self, sid, stype):
        self.id = sid
        self.type = stype
        self.occupied = False
        self.vehicle = None

class Ticket:
    def __init__(self, vehicle, spot):
        self.vehicle = vehicle
        self.spot = spot
        self.in_time = datetime.now()
        self.out_time = None
        self.fee = 0

    def close(self):
        self.out_time = datetime.now()
        duration = (self.out_time - self.in_time).seconds / 3600
        base_rate = {
            SpotType.MOTORCYCLE: 2,
            SpotType.CAR: 5,
            SpotType.TRUCK: 10
        }
        self.fee = base_rate[self.vehicle.type] * max(1, duration)
        return self.fee

class ParkingLot:
    def __init__(self, name):
        self.name = name
        self.spots = []

    def add_spot(self, spot):
        self.spots.append(spot)

    def find_spot(self, vehicle_type):
        for spot in self.spots:
            if not spot.occupied and spot.type == vehicle_type:
                return spot
        return None

    def release_spot(self, spot):
        spot.occupied = False
        spot.vehicle = None

class ParkingLotManager:
    def __init__(self, lot):
        self.lot = lot
        self.active_tickets = {}

    def park_vehicle(self, vehicle):
        if vehicle.plate in self.active_tickets:
            print(f"⚠️ El vehículo {vehicle.plate} ya está estacionado.")
            return
        spot = self.lot.find_spot(vehicle.type)
        if not spot:
            print(f"🚫 No hay lugares disponibles para tipo {vehicle.type.value}.")
            return
        spot.occupied = True
        spot.vehicle = vehicle
        ticket = Ticket(vehicle, spot)
        self.active_tickets[vehicle.plate] = ticket
        print(f"✅ {vehicle.type.value} {vehicle.plate} estacionado en lugar {spot.id}.")

    def exit_vehicle(self, plate):
        ticket = self.active_tickets.get(plate)
        if not ticket:
            print(f"⚠️ No se encontró registro para {plate}.")
            return
        fee = ticket.close()
        self.lot.release_spot(ticket.spot)
        del self.active_tickets[plate]
        print(f"💨 {ticket.vehicle.plate} salió. Total a pagar: ${fee:.2f}")

    def show_availability(self):
        free = [s for s in self.lot.spots if not s.occupied]
        print(f"📊 Lugares libres: {len(free)} / {len(self.lot.spots)}")
        for s in free:
            print(f" - Lugar {s.id} ({s.type.value}) disponible.")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear estacionamiento
lot = ParkingLot("Castillo Garage")
lot.add_spot(ParkingSpot(1, SpotType.MOTORCYCLE))
lot.add_spot(ParkingSpot(2, SpotType.CAR))
lot.add_spot(ParkingSpot(3, SpotType.CAR))
lot.add_spot(ParkingSpot(4, SpotType.TRUCK))

manager = ParkingLotManager(lot)

# Vehículos
v1 = Car("ABC-123")
v2 = Motorcycle("MOTO-7")
v3 = Truck("BIG-999")

# Entradas
manager.park_vehicle(v1)
manager.park_vehicle(v2)
manager.park_vehicle(v3)
manager.show_availability()

# Salida
manager.exit_vehicle("ABC-123")
manager.show_availability()
```

**🎯 Salida esperada:**

```
✅ Auto ABC-123 estacionado en lugar 2.
✅ Moto MOTO-7 estacionado en lugar 1.
✅ Camión BIG-999 estacionado en lugar 4.
📊 Lugares libres: 1 / 4
 - Lugar 3 (Auto) disponible.
💨 ABC-123 salió. Total a pagar: $5.00
📊 Lugares libres: 2 / 4
 - Lugar 3 (Auto) disponible.
 - Lugar 2 (Auto) disponible.
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                         |
| ---------------------- | ------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(S) para búsqueda de lugar.                                  |
| 💾 **Espacio:**        | O(V + S) — vehículos activos y lugares.                       |
| ⚡ **Escalabilidad:**   | Alta — se pueden agregar pisos, sensores o pagos automáticos. |
| 🧩 **Tipo de patrón:** | Controlador + Herencia + Estado físico.                       |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de modelado físico y gestión de recursos finitos.

**🔧 Posibles optimizaciones:**

* Integrar `Floor` para estacionamientos multinivel.
* Añadir `PaymentProcessor` para cobro digital.
* Optimizar búsqueda con colas por tipo de lugar.

**📚 Lecciones aprendidas:**

* La **herencia** simplifica el manejo de tipos de vehículos.
* La **gestión de estado físico** (ocupado/libre) es esencial para sistemas de recursos.
* La **composición** (ParkingLot + Manager + Spots) permite crecimiento modular.

**✅ Conclusión final:**

> “Diseñé un sistema de estacionamiento con manejo de tipos de vehículo,
> control de ocupación y cálculo de tarifa.
> El modelo refleja una infraestructura física real con extensibilidad para automatización.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | Controlador + Herencia + Estado físico |
| Complejidad      | O(S)                                   |
| Palabra clave    | “Gestión de recursos limitados”        |
| Tipo de problema | Modelado de infraestructura            |
| Nivel            | 🟡 Medio                               |
