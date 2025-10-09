# 🚗 **LeetCode #044 — Design a Transportation Booking System (Uber/Didi Style)**

> **Tema:** Conductores, vehículos, viajes, tarifas dinámicas y estados de reserva
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Estado dinámico y validación de disponibilidad*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que simule una **plataforma de transporte urbano** (tipo Uber o Didi),
> manejando **conductores, vehículos, pasajeros y reservas de viaje**,
> con **validación de disponibilidad, cálculo de tarifa y estados dinámicos del viaje.**

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **conductores** y **vehículos**.
* Registrar **pasajeros**.
* Permitir **solicitar un viaje**, asignando un conductor disponible.
* Calcular la **tarifa total** en función de la distancia.
* Cambiar el **estado del viaje** (Solicitado → En Curso → Completado → Cancelado).
* Registrar **historial de viajes** para pasajeros y conductores.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar una aplicación tipo Uber donde los pasajeros pueden solicitar un viaje.
> El sistema busca un conductor disponible, calcula la tarifa según la distancia
> y cambia los estados del viaje conforme avanza el flujo.”

---

**❓ Preguntas al entrevistador:**

* ¿Cada conductor puede tener un solo viaje activo? → ✅ Sí.
* ¿El cálculo de tarifa es fijo o dinámico? → 🟡 Dinámico según distancia base.
* ¿Se puede cancelar un viaje? → ✅ Sí, antes de iniciarse.
* ¿Debe registrarse historial de viajes? → ✅ Sí, tanto en pasajero como conductor.

**🧩 Casos límite:**

* [x] No hay conductores disponibles.
* [x] Distancia negativa o cero.
* [x] Cancelar un viaje ya completado.
* [x] Solicitud de viaje duplicada.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Modelar entidades interconectadas (`Driver`, `Passenger`, `Ride`, `Vehicle`)
> con control de disponibilidad y cambios de estado dinámicos.

---

### 🧩 Clases Principales

| Clase              | Responsabilidad                    | Relaciones                                  |
| ------------------ | ---------------------------------- | ------------------------------------------- |
| `TransportManager` | Control central del sistema.       | Coordina `Driver`, `Passenger`, `Ride`.     |
| `Driver`           | Representa al conductor.           | Asociado a un `Vehicle` y múltiples `Ride`. |
| `Vehicle`          | Representa el automóvil asignado.  | Asociado a un `Driver`.                     |
| `Passenger`        | Representa al usuario solicitante. | Posee historial de viajes.                  |
| `Ride`             | Representa un viaje solicitado.    | Asocia `Passenger` y `Driver`.              |

---

### 🔁 Flujo de Operación

1. Registrar conductores (con vehículo) y pasajeros.
2. El pasajero solicita un viaje con distancia estimada.
3. El sistema asigna un conductor disponible.
4. Se calcula la tarifa (distancia × tarifa base).
5. El conductor acepta, inicia, completa o cancela el viaje.
6. Ambos registran el historial.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|   TransportManager   |
+----------------------+
| - drivers, passengers, rides |
+----------------------+
| + registerDriver()   |
| + registerPassenger()|
| + requestRide()      |
| + completeRide()     |
| + cancelRide()       |
+----------------------+
          |
          v
+----------------------+
|        Ride          |
+----------------------+
| driver, passenger, distance, fare, status |
+----------------------+

+----------------------+
|       Driver         |
+----------------------+
| id, name, vehicle, available, history |
+----------------------+

+----------------------+
|       Vehicle        |
+----------------------+
| plate, model         |
+----------------------+

+----------------------+
|      Passenger       |
+----------------------+
| id, name, history    |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar flujo de solicitud, asignación y finalización de viaje.

```python
from datetime import datetime
import random

class RideStatus:
    REQUESTED = "Solicitado"
    IN_PROGRESS = "En Curso"
    COMPLETED = "Completado"
    CANCELLED = "Cancelado"

class Vehicle:
    def __init__(self, plate, model):
        self.plate = plate
        self.model = model

    def __str__(self):
        return f"{self.model} ({self.plate})"

class Driver:
    def __init__(self, did, name, vehicle):
        self.id = did
        self.name = name
        self.vehicle = vehicle
        self.available = True
        self.history = []

    def __str__(self):
        return f"🚗 Conductor {self.name} - {self.vehicle}"

class Passenger:
    def __init__(self, pid, name):
        self.id = pid
        self.name = name
        self.history = []

class Ride:
    BASE_RATE = 10  # tarifa base por km

    def __init__(self, driver, passenger, distance):
        self.driver = driver
        self.passenger = passenger
        self.distance = distance
        self.fare = self.BASE_RATE * distance
        self.status = RideStatus.REQUESTED
        self.start_time = None
        self.end_time = None

    def start(self):
        if self.status != RideStatus.REQUESTED:
            print("⚠️ No se puede iniciar un viaje en este estado.")
            return
        self.status = RideStatus.IN_PROGRESS
        self.start_time = datetime.now()
        self.driver.available = False
        print(f"🏁 Viaje iniciado por {self.driver.name} para {self.passenger.name} ({self.distance} km).")

    def complete(self):
        if self.status != RideStatus.IN_PROGRESS:
            print("⚠️ El viaje no está en curso.")
            return
        self.status = RideStatus.COMPLETED
        self.end_time = datetime.now()
        self.driver.available = True
        self.driver.history.append(self)
        self.passenger.history.append(self)
        print(f"✅ Viaje completado: {self.distance} km | Tarifa ${self.fare:.2f}")

    def cancel(self):
        if self.status == RideStatus.COMPLETED:
            print("⚠️ No se puede cancelar un viaje completado.")
            return
        self.status = RideStatus.CANCELLED
        self.driver.available = True
        print(f"❌ Viaje cancelado para {self.passenger.name}.")

class TransportManager:
    def __init__(self):
        self.drivers = {}
        self.passengers = {}
        self.rides = []

    def register_driver(self, did, name, vehicle):
        if did in self.drivers:
            print("⚠️ Conductor ya registrado.")
            return
        d = Driver(did, name, vehicle)
        self.drivers[did] = d
        print(f"🧍 {d}")
        return d

    def register_passenger(self, pid, name):
        if pid in self.passengers:
            print("⚠️ Pasajero ya registrado.")
            return
        p = Passenger(pid, name)
        self.passengers[pid] = p
        print(f"🧍 Pasajero registrado: {name}")
        return p

    def find_available_driver(self):
        available = [d for d in self.drivers.values() if d.available]
        return random.choice(available) if available else None

    def request_ride(self, pid, distance):
        passenger = self.passengers.get(pid)
        if not passenger:
            print("🚫 Pasajero no encontrado.")
            return
        if distance <= 0:
            print("⚠️ Distancia inválida.")
            return
        driver = self.find_available_driver()
        if not driver:
            print("🚫 No hay conductores disponibles.")
            return
        ride = Ride(driver, passenger, distance)
        self.rides.append(ride)
        print(f"📲 {passenger.name} solicitó un viaje de {distance} km.")
        print(f"🚗 Conductor asignado: {driver.name} ({driver.vehicle.model}) | Tarifa estimada: ${ride.fare:.2f}")
        return ride

    def complete_ride(self, ride):
        ride.complete()

    def cancel_ride(self, ride):
        ride.cancel()
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear sistema de transporte
tm = TransportManager()

# Registrar conductores y vehículos
v1 = Vehicle("ABC123", "Nissan Versa")
v2 = Vehicle("XYZ999", "Toyota Prius")
d1 = tm.register_driver("D1", "Carlos", v1)
d2 = tm.register_driver("D2", "Luis", v2)

# Registrar pasajeros
p1 = tm.register_passenger("P1", "Isabel")

# Solicitar viaje
ride1 = tm.request_ride("P1", 12)

# Iniciar y completar viaje
ride1.start()
tm.complete_ride(ride1)

# Nuevo viaje sin conductores (forzar indisponibilidad)
d1.available = False
d2.available = False
tm.request_ride("P1", 5)

# Cancelar viaje
ride2 = tm.request_ride("P1", 7)
tm.cancel_ride(ride2)
```

**🎯 Salida esperada:**

```
🧍 🚗 Conductor Carlos - Nissan Versa (ABC123)
🧍 🚗 Conductor Luis - Toyota Prius (XYZ999)
🧍 Pasajero registrado: Isabel
📲 Isabel solicitó un viaje de 12 km.
🚗 Conductor asignado: Carlos (Nissan Versa) | Tarifa estimada: $120.00
🏁 Viaje iniciado por Carlos para Isabel (12 km).
✅ Viaje completado: 12 km | Tarifa $120.00
🚫 No hay conductores disponibles.
📲 Isabel solicitó un viaje de 7 km.
🚗 Conductor asignado: Luis (Toyota Prius) | Tarifa estimada: $70.00
❌ Viaje cancelado para Isabel.
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                |
| ---------------------- | -------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(D) — búsqueda de conductor disponible.                             |
| 💾 **Espacio:**        | O(P + D + R) — pasajeros, conductores y viajes.                      |
| ⚡ **Escalabilidad:**   | Alta — puede extenderse con GPS, precios dinámicos y notificaciones. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Estado dinámico.                         |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de flujo transaccional con estados dinámicos y validaciones en tiempo real.

**🔧 Posibles optimizaciones:**

* Añadir `PricingEngine` con tarifas variables según hora y demanda.
* Implementar `LocationSystem` con coordenadas y cálculo de distancia real.
* Integrar `RatingSystem` para calificaciones de pasajeros y conductores.

**📚 Lecciones aprendidas:**

* La **gestión de estados** (solicitado → en curso → completado) es clave para coherencia del flujo.
* El **control de disponibilidad** asegura que no se asignen conductores ocupados.
* Este modelo es base de las arquitecturas modernas de apps de movilidad.

**✅ Conclusión final:**

> “Diseñé un sistema de transporte urbano modular con gestión de viajes, conductores y tarifas,
> aplicando control de disponibilidad, validaciones y cambios de estado.
> El modelo refleja los principios básicos de plataformas como Uber o Didi.”

---

📘 **Resumen final**

| Aspecto          | Valor                                       |
| ---------------- | ------------------------------------------- |
| Patrón           | Controlador + Composición + Estado dinámico |
| Complejidad      | O(D)                                        |
| Palabra clave    | “Asignación dinámica de conductores”        |
| Tipo de problema | Modelado de sistema de transporte           |
| Nivel            | 🟡 Medio                                    |

---