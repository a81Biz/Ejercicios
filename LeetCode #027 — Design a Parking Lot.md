# 🚗 **LeetCode #027 — Design a Parking Lot**

> **Tema:** Modelado de sistemas físicos con clases y relaciones jerárquicas
> **Nivel:** 🟢 Fácil
> **Patrón:** *Herencia + Polimorfismo + Encapsulación de reglas*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP para un **estacionamiento** capaz de manejar vehículos de distintos tipos, calcular tarifas y administrar espacios disponibles.

---

**📖 Enunciado resumido:**
Diseñar un conjunto de clases que representen un **Parking Lot** con las siguientes características:

* Soporta **distintos tipos de vehículos** (auto, motocicleta, camión).
* Cada tipo ocupa un número distinto de espacios.
* La **entrada y salida** de vehículos debe actualizar la disponibilidad.
* Se cobra una tarifa según el tipo de vehículo o duración de la estancia.
* El sistema debe ser extensible y fácil de mantener.

---

**💬 Reexplicación en voz alta:**

> “Necesito un conjunto de clases que modelen los componentes de un estacionamiento real: espacios, vehículos y administración.
> El `ParkingLot` será el controlador principal; `Vehicle` una jerarquía con herencia; y `Ticket` representará la transacción.”

---

**❓ Preguntas al entrevistador:**

* ¿Debo considerar múltiples niveles o pisos? → 🟡 Opcional.
* ¿Las tarifas son fijas o por tiempo? → 🟢 Fijas por tipo de vehículo (simplificación).
* ¿Debo manejar un límite de espacios? → ✅ Sí.
* ¿Qué pasa si el estacionamiento está lleno? → ❌ No se permite ingreso.

**🧩 Casos límite:**

* [x] Lote lleno.
* [x] Vehículo desconocido.
* [x] Salida sin ticket.
* [x] Espacios reservados para tipo distinto.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Representar jerarquías naturales (vehículos) y control de disponibilidad (parking).

---

### 🧩 Clases Principales

| Clase         | Responsabilidad                                         | Relaciones                                 |
| ------------- | ------------------------------------------------------- | ------------------------------------------ |
| `ParkingLot`  | Controla entrada/salida y administración de espacios.   | Contiene `ParkingSpot` y genera `Ticket`.  |
| `ParkingSpot` | Representa un espacio de estacionamiento individual.    | Contiene referencia a `Vehicle`.           |
| `Vehicle`     | Clase base abstracta para distintos tipos de vehículos. | Heredada por `Car`, `Motorcycle`, `Truck`. |
| `Ticket`      | Representa la asignación de un vehículo a un espacio.   | Asociado a `Vehicle`.                      |

---

### 🔁 Flujo de Operación

1. Un vehículo llega → `ParkingLot.assign_spot(vehicle)`.
2. Si hay espacio compatible → se genera `Ticket`.
3. Al salir → `ParkingLot.release_spot(ticket)`.
4. Se actualiza la disponibilidad y se calcula la tarifa.

---

### 🧱 Relaciones UML (simplificadas)

```
+------------------+
|   ParkingLot     |
+------------------+
| - spots: list    |
| - tickets: list  |
+------------------+
| + assignSpot()   |
| + releaseSpot()  |
| + getAvailable() |
+------------------+
          |
          v
+------------------+
|   ParkingSpot    |
+------------------+
| - id             |
| - type           |
| - occupied       |
| - vehicle        |
+------------------+

+------------------+
|     Vehicle      |
+------------------+
| - plateNumber    |
| - type           |
+------------------+
        /|\
         |
   +-----------+
   |   Car     |
   | Motorcycle|
   |   Truck   |
   +-----------+

+------------------+
|     Ticket       |
+------------------+
| - vehicle        |
| - spotId         |
| - startTime      |
| - endTime        |
+------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar un modelo modular, legible y escalable.

```python
from datetime import datetime

class Vehicle:
    def __init__(self, plate_number, type):
        self.plate_number = plate_number
        self.type = type

class Car(Vehicle):
    def __init__(self, plate_number):
        super().__init__(plate_number, "Car")

class Motorcycle(Vehicle):
    def __init__(self, plate_number):
        super().__init__(plate_number, "Motorcycle")

class Truck(Vehicle):
    def __init__(self, plate_number):
        super().__init__(plate_number, "Truck")


class ParkingSpot:
    def __init__(self, spot_id, type):
        self.spot_id = spot_id
        self.type = type
        self.occupied = False
        self.vehicle = None

    def assign_vehicle(self, vehicle):
        if not self.occupied and self.type == vehicle.type:
            self.vehicle = vehicle
            self.occupied = True
            return True
        return False

    def release_vehicle(self):
        self.vehicle = None
        self.occupied = False


class Ticket:
    def __init__(self, vehicle, spot_id):
        self.vehicle = vehicle
        self.spot_id = spot_id
        self.start_time = datetime.now()
        self.end_time = None

    def close(self):
        self.end_time = datetime.now()

    def get_duration(self):
        if self.end_time:
            return (self.end_time - self.start_time).seconds / 3600
        return 0


class ParkingLot:
    def __init__(self):
        self.spots = []
        self.tickets = []

    def add_spot(self, spot):
        self.spots.append(spot)

    def assign_spot(self, vehicle):
        for spot in self.spots:
            if spot.assign_vehicle(vehicle):
                ticket = Ticket(vehicle, spot.spot_id)
                self.tickets.append(ticket)
                print(f"{vehicle.type} asignado al lugar {spot.spot_id}")
                return ticket
        print("No hay lugares disponibles para este tipo de vehículo.")
        return None

    def release_spot(self, ticket):
        ticket.close()
        for spot in self.spots:
            if spot.spot_id == ticket.spot_id:
                spot.release_vehicle()
                print(f"Vehículo liberado del lugar {spot.spot_id}.")
                break
        cost = self.calculate_fee(ticket)
        print(f"Total a pagar: ${cost:.2f}")
        return cost

    def calculate_fee(self, ticket):
        rates = {"Car": 10, "Motorcycle": 5, "Truck": 20}
        return rates[ticket.vehicle.type]

    def get_available(self):
        return len([s for s in self.spots if not s.occupied])
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear estacionamiento
lot = ParkingLot()
lot.add_spot(ParkingSpot(1, "Car"))
lot.add_spot(ParkingSpot(2, "Motorcycle"))
lot.add_spot(ParkingSpot(3, "Truck"))

# Entradas
car1 = Car("ABC123")
ticket1 = lot.assign_spot(car1)

moto1 = Motorcycle("XYZ777")
ticket2 = lot.assign_spot(moto1)

# Salida
lot.release_spot(ticket1)
lot.release_spot(ticket2)
```

**🎯 Salida esperada:**

```
Car asignado al lugar 1
Motorcycle asignado al lugar 2
Vehículo liberado del lugar 1.
Total a pagar: $10.00
Vehículo liberado del lugar 2.
Total a pagar: $5.00
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                            |
| ---------------------- | ---------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) por búsqueda de lugar.                                      |
| 💾 **Espacio:**        | O(N) — depende de los espacios definidos.                        |
| ⚡ **Escalabilidad:**   | Alta — se pueden agregar niveles, sensores, o tarifas dinámicas. |
| 🧩 **Tipo de patrón:** | Herencia + Composición.                                          |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar entendimiento de herencia, composición y abstracción de entidades reales.

**🔧 Posibles optimizaciones:**

* Implementar un patrón `Factory` para crear vehículos.
* Agregar un `RateStrategy` para tarifas variables.
* Manejar múltiples niveles con `ParkingFloor`.

**📚 Lecciones aprendidas:**

* La **herencia** permite modelar jerarquías de objetos reales.
* La **composición** mantiene independencia entre componentes.
* El **control de estado** (ocupado/libre) es crucial en sistemas físicos.

**✅ Conclusión final:**

> “Diseñé un estacionamiento modular que representa entidades reales mediante herencia y encapsulación.
> El sistema permite extensión sin romper la arquitectura base,
> demostrando comprensión práctica de los principios OOP.”

---

📘 **Resumen final**

| Aspecto          | Valor                      |
| ---------------- | -------------------------- |
| Patrón           | Herencia + Composición     |
| Complejidad      | O(N)                       |
| Palabra clave    | “Jerarquía y estado”       |
| Tipo de problema | Modelado de sistema físico |
| Nivel            | 🟢 Fácil                   |

---

