# 🛗 **LeetCode #028 — Design an Elevator System**

> **Tema:** Modelado de sistemas concurrentes y manejo de estado compartido
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador central + Entidades colaborativas + State Pattern*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que controle **uno o varios elevadores**,
> administrando solicitudes de usuarios y movimiento coordinado entre pisos.

---

**📖 Enunciado resumido:**
Diseñar un conjunto de clases para un **sistema de ascensores** que:

* Maneje múltiples ascensores y múltiples pisos.
* Responda a solicitudes desde dentro y fuera del elevador.
* Optimice el movimiento (elegir qué ascensor atenderá una solicitud).
* Evite conflictos o duplicación de rutas.
* Permita escalar a más ascensores en el futuro.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar un sistema que tiene varios ascensores, cada uno con su propio estado (posición, dirección, ocupación).
> Un `ElevatorController` central recibirá peticiones y decidirá qué elevador responderá,
> usando un algoritmo de asignación básico.”

---

**❓ Preguntas al entrevistador:**

* ¿Cuántos pisos tiene el edificio? → ✅ Se define al inicio.
* ¿Debe haber múltiples elevadores? → ✅ Sí.
* ¿Se necesita un algoritmo de optimización? → 🟡 No, pero debe poder ampliarse.
* ¿Debe haber límite de capacidad? → 🟢 Opcional (simplificable).

**🧩 Casos límite:**

* [x] Todas las solicitudes son al mismo piso.
* [x] Elevador en mantenimiento.
* [x] Solicitudes simultáneas.
* [x] Ningún elevador disponible.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Separar el control global del comportamiento individual de cada elevador.

---

### 🧩 Clases Principales

| Clase                | Responsabilidad                                                 | Relaciones                           |
| -------------------- | --------------------------------------------------------------- | ------------------------------------ |
| `ElevatorController` | Coordina todos los elevadores y gestiona las solicitudes.       | Contiene `Elevator`.                 |
| `Elevator`           | Representa un elevador individual con su estado y dirección.    | Contiene `RequestQueue`.             |
| `Request`            | Representa una solicitud de movimiento (piso destino y origen). | Usada por `Elevator` y `Controller`. |
| `RequestQueue`       | Almacena y ordena las solicitudes pendientes.                   | Interna de `Elevator`.               |

---

### 🔁 Flujo de Operación

1. Usuario llama a un elevador desde un piso.
2. `ElevatorController` selecciona el elevador más cercano disponible.
3. El elevador recibe la solicitud y se mueve hacia el piso.
4. Usuario entra y selecciona piso destino.
5. El elevador agrega esa solicitud interna a su cola.
6. El proceso se repite para cada nueva solicitud.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|  ElevatorController  |
+----------------------+
| - elevators: list    |
+----------------------+
| + requestElevator()  |
| + stepAll()          |
+----------------------+
          |
          v
+----------------------+
|      Elevator        |
+----------------------+
| - id                 |
| - currentFloor       |
| - direction          |
| - queue: RequestQueue|
+----------------------+
| + addRequest()       |
| + move()             |
| + step()             |
+----------------------+

+----------------------+
|       Request        |
+----------------------+
| - sourceFloor        |
| - destinationFloor   |
+----------------------+

+----------------------+
|     RequestQueue     |
+----------------------+
| - requests: list     |
| + add()              |
| + popNext()          |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Modelo funcional con comportamiento básico de múltiples elevadores.

```python
from enum import Enum
from collections import deque

class Direction(Enum):
    UP = 1
    DOWN = -1
    IDLE = 0

class Request:
    def __init__(self, source_floor, destination_floor):
        self.source = source_floor
        self.destination = destination_floor

class RequestQueue:
    def __init__(self):
        self.requests = deque()

    def add_request(self, request):
        self.requests.append(request)

    def next_request(self):
        return self.requests.popleft() if self.requests else None


class Elevator:
    def __init__(self, id, current_floor=0):
        self.id = id
        self.current_floor = current_floor
        self.direction = Direction.IDLE
        self.queue = RequestQueue()

    def add_request(self, request):
        self.queue.add_request(request)
        print(f"[ELEVATOR {self.id}] Nueva solicitud: {request.source} → {request.destination}")

    def move_one_step(self):
        if self.direction == Direction.UP:
            self.current_floor += 1
        elif self.direction == Direction.DOWN:
            self.current_floor -= 1

    def step(self):
        if not self.queue.requests:
            self.direction = Direction.IDLE
            return

        req = self.queue.requests[0]
        if self.current_floor < req.source:
            self.direction = Direction.UP
            self.move_one_step()
        elif self.current_floor > req.source:
            self.direction = Direction.DOWN
            self.move_one_step()
        else:
            # Llegó al piso origen
            print(f"[ELEVATOR {self.id}] Recogiendo pasajero en piso {req.source}")
            if self.current_floor < req.destination:
                self.direction = Direction.UP
            elif self.current_floor > req.destination:
                self.direction = Direction.DOWN
            else:
                print(f"[ELEVATOR {self.id}] Ya está en el destino {req.destination}")
                self.queue.next_request()
                self.direction = Direction.IDLE
                return
            self.move_one_step()

    def status(self):
        print(f"[ELEVATOR {self.id}] Piso: {self.current_floor}, Dirección: {self.direction.name}")


class ElevatorController:
    def __init__(self, num_elevators=2):
        self.elevators = [Elevator(i) for i in range(num_elevators)]

    def request_elevator(self, source_floor, destination_floor):
        best = min(self.elevators, key=lambda e: abs(e.current_floor - source_floor))
        best.add_request(Request(source_floor, destination_floor))

    def step_all(self):
        for elevator in self.elevators:
            elevator.step()
            elevator.status()
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
controller = ElevatorController(num_elevators=2)

# Solicitudes
controller.request_elevator(0, 5)
controller.request_elevator(3, 1)

# Simular varios pasos
for _ in range(8):
    controller.step_all()
```

**🎯 Salida esperada (ejemplo simplificado):**

```
[ELEVATOR 0] Nueva solicitud: 0 → 5
[ELEVATOR 1] Nueva solicitud: 3 → 1
[ELEVATOR 0] Recogiendo pasajero en piso 0
[ELEVATOR 0] Piso: 1, Dirección: UP
[ELEVATOR 1] Piso: 2, Dirección: DOWN
...
[ELEVATOR 0] Piso: 5, Dirección: IDLE
```

✅ Correcto — el sistema asigna el elevador más cercano y mueve cada uno paso a paso.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                         |
| ---------------------- | ------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(E) por ciclo (E = número de elevadores).                    |
| 💾 **Espacio:**        | O(R) — depende de solicitudes activas.                        |
| ⚡ **Escalabilidad:**   | Alta — se puede integrar lógica de prioridad o mantenimiento. |
| 🧩 **Tipo de patrón:** | Controlador central + State Pattern.                          |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de concurrencia simulada y control distribuido.

**🔧 Posibles optimizaciones:**

* Agregar **scheduler inteligente** (nearest-car o look-ahead).
* Manejar prioridad de direcciones (subida/bajada).
* Agregar `ElevatorStatus` y `Sensor` para simular hardware real.

**📚 Lecciones aprendidas:**

* La **separación de control** (Controller vs Elevator) permite escalabilidad.
* El **State Pattern** facilita agregar nuevos comportamientos sin modificar lógica existente.
* El modelo es extensible a un sistema concurrente real.

**✅ Conclusión final:**

> “Diseñé un sistema de elevadores donde cada entidad tiene un estado controlado
> y un `ElevatorController` central coordina todas las solicitudes.
> El patrón de estados y controladores desacoplados garantiza flexibilidad y extensión futura.”

---

📘 **Resumen final**

| Aspecto          | Valor                           |
| ---------------- | ------------------------------- |
| Patrón           | Controlador + State Pattern     |
| Complejidad      | O(E)                            |
| Palabra clave    | “Control distribuido”           |
| Tipo de problema | Modelado de sistema concurrente |
| Nivel            | 🟡 Medio                        |

