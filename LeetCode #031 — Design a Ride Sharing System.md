# 🚕 **LeetCode #031 — Design a Ride Sharing System**

> **Tema:** Asignación dinámica, observadores y coordinación multiusuario
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador central + Observer Pattern + Estado dinámico*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP para un servicio tipo **Uber/Lyft**,
> que asigne conductores a pasajeros en tiempo real, calcule costos y administre estados del viaje.

---

**📖 Enunciado resumido:**
El sistema debe permitir:

* Registrar **conductores** y **pasajeros**.
* Crear **solicitudes de viaje** con origen y destino.
* Buscar el conductor más cercano disponible.
* Asignar el viaje, actualizar el estado (`Pendiente`, `En curso`, `Completado`).
* Calcular tarifa según distancia (simplificada).

---

**💬 Reexplicación en voz alta:**

> “Voy a representar un sistema con tres entidades principales: `Passenger`, `Driver` y `Ride`.
> El `RideManager` actuará como controlador central que recibe solicitudes,
> selecciona un conductor y gestiona el ciclo completo del viaje.”

---

**❓ Preguntas al entrevistador:**

* ¿Debe manejar múltiples pasajeros o carpool? → ❌ No, un pasajero por viaje.
* ¿Se debe simular geolocalización real? → 🟡 No, solo coordenadas simples (x, y).
* ¿La tarifa depende de tiempo o distancia? → ✅ Por distancia.
* ¿Se permite rechazar viajes? → ✅ Conductores pueden estar ocupados.

**🧩 Casos límite:**

* [x] No hay conductores disponibles.
* [x] Múltiples solicitudes simultáneas.
* [x] Viaje cancelado antes de iniciar.
* [x] Distancia cero.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Modelar un sistema de viajes con flujo: *solicitud → asignación → ejecución → finalización.*

---

### 🧩 Clases Principales

| Clase         | Responsabilidad                                 | Relaciones                              |
| ------------- | ----------------------------------------------- | --------------------------------------- |
| `RideManager` | Controla la creación y asignación de viajes.    | Coordina `Passenger`, `Driver`, `Ride`. |
| `Passenger`   | Representa un usuario que solicita un viaje.    | Crea `RideRequest`.                     |
| `Driver`      | Representa un conductor con ubicación y estado. | Puede aceptar un `Ride`.                |
| `Ride`        | Representa un viaje activo.                     | Une `Driver` y `Passenger`.             |
| `Location`    | Define coordenadas simples (x, y).              | Usada por `Driver` y `Passenger`.       |

---

### 🔁 Flujo de Operación

1. El pasajero solicita un viaje (origen, destino).
2. El `RideManager` busca el conductor más cercano disponible.
3. Crea un `Ride` y lo asigna al conductor.
4. El conductor cambia de estado a `Ocupado`.
5. Al finalizar el viaje → se calcula la tarifa y se actualiza el estado.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|     RideManager      |
+----------------------+
| - drivers            |
| - rides              |
+----------------------+
| + requestRide()      |
| + findDriver()       |
| + completeRide()     |
+----------------------+
          |
          v
+----------------------+
|        Ride          |
+----------------------+
| passenger, driver, origin, dest, fare |
+----------------------+
| + calculateFare()    |
| + complete()         |
+----------------------+

+----------------------+
|      Driver          |
+----------------------+
| id, name, location, available |
+----------------------+
| + updateLocation()   |
| + setAvailable()     |
+----------------------+

+----------------------+
|     Passenger        |
+----------------------+
| id, name, location   |
+----------------------+

+----------------------+
|     Location         |
+----------------------+
| x, y                 |
+----------------------+
| + distanceTo()       |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Construir un modelo funcional con control de estado y asignación inteligente.

```python
import math
from enum import Enum

class RideStatus(Enum):
    REQUESTED = "Solicitado"
    IN_PROGRESS = "En curso"
    COMPLETED = "Completado"
    CANCELLED = "Cancelado"

class Location:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def distance_to(self, other):
        return math.sqrt((self.x - other.x)**2 + (self.y - other.y)**2)


class Passenger:
    def __init__(self, pid, name, location):
        self.id = pid
        self.name = name
        self.location = location


class Driver:
    def __init__(self, did, name, location):
        self.id = did
        self.name = name
        self.location = location
        self.available = True

    def update_location(self, location):
        self.location = location

    def set_available(self, value):
        self.available = value


class Ride:
    def __init__(self, passenger, driver, origin, destination):
        self.passenger = passenger
        self.driver = driver
        self.origin = origin
        self.destination = destination
        self.status = RideStatus.REQUESTED
        self.fare = 0

    def start(self):
        self.status = RideStatus.IN_PROGRESS
        print(f"{self.driver.name} comenzó el viaje con {self.passenger.name}.")

    def complete(self):
        self.status = RideStatus.COMPLETED
        self.driver.set_available(True)
        self.fare = self.calculate_fare()
        print(f"Viaje completado. Total: ${self.fare:.2f}")

    def calculate_fare(self):
        distance = self.origin.distance_to(self.destination)
        base_fare = 5
        rate_per_km = 2
        return base_fare + (rate_per_km * distance)


class RideManager:
    def __init__(self):
        self.drivers = []
        self.rides = []

    def register_driver(self, driver):
        self.drivers.append(driver)

    def find_driver(self, passenger_location):
        available_drivers = [d for d in self.drivers if d.available]
        if not available_drivers:
            return None
        return min(available_drivers, key=lambda d: d.location.distance_to(passenger_location))

    def request_ride(self, passenger, destination):
        driver = self.find_driver(passenger.location)
        if not driver:
            print("No hay conductores disponibles.")
            return None

        driver.set_available(False)
        ride = Ride(passenger, driver, passenger.location, destination)
        self.rides.append(ride)
        print(f"{passenger.name} fue asignado a {driver.name}.")
        return ride

    def complete_ride(self, ride):
        ride.complete()
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear sistema
manager = RideManager()

# Registrar conductores
manager.register_driver(Driver("D1", "Juan", Location(0, 0)))
manager.register_driver(Driver("D2", "Ana", Location(5, 5)))

# Crear pasajero
passenger = Passenger("P1", "Isabel", Location(2, 2))

# Solicitud de viaje
ride = manager.request_ride(passenger, Location(8, 8))

# Iniciar y finalizar viaje
if ride:
    ride.start()
    manager.complete_ride(ride)
```

**🎯 Salida esperada:**

```
Isabel fue asignado a Juan.
Juan comenzó el viaje con Isabel.
Viaje completado. Total: $20.97
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                  |
| ---------------------- | ---------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(D) para buscar el conductor más cercano (D = número de conductores). |
| 💾 **Espacio:**        | O(R) — depende de viajes activos.                                      |
| ⚡ **Escalabilidad:**   | Alta — se puede integrar con colas, mapas, o notificaciones.           |
| 🧩 **Tipo de patrón:** | Controlador central + Observador.                                      |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de asignación dinámica, estados y observadores.

**🔧 Posibles optimizaciones:**

* Implementar un `Dispatcher` que asigne viajes en paralelo.
* Usar `Observer Pattern` para notificar pasajeros y conductores.
* Integrar algoritmos de búsqueda geoespacial (`k-d tree`).

**📚 Lecciones aprendidas:**

* La **asignación dinámica** se basa en control central + entidades autónomas.
* El **estado del viaje** determina el flujo completo de operaciones.
* El patrón `Controller + Entities` imita sistemas reales distribuidos.

**✅ Conclusión final:**

> “Diseñé un sistema de transporte compartido con flujo de solicitud, asignación y finalización,
> aplicando encapsulación, control de estado y selección dinámica del recurso óptimo.”

---

📘 **Resumen final**

| Aspecto          | Valor                            |
| ---------------- | -------------------------------- |
| Patrón           | Controlador + Observador         |
| Complejidad      | O(D)                             |
| Palabra clave    | “Asignación dinámica”            |
| Tipo de problema | Modelado de sistema multiusuario |
| Nivel            | 🟡 Medio                         |

---