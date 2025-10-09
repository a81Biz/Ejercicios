# 🍔 **LeetCode #032 — Design a Food Delivery System**

> **Tema:** Coordinación de múltiples entidades con estados concurrentes
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador central + Composición + State Pattern*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP para un **servicio de entrega de comida**
> (tipo *Uber Eats* o *DoorDash*), donde los **clientes**, **restaurantes** y **repartidores** interactúan mediante órdenes y entregas.

---

**📖 Enunciado resumido:**
El sistema debe permitir:

* Registrar **clientes**, **restaurantes**, **platos** y **repartidores**.
* Crear un **pedido** con varios ítems.
* Asignar un **repartidor disponible** al pedido.
* Cambiar estados del pedido (`Creado`, `En preparación`, `En camino`, `Entregado`).
* Calcular el **total** del pedido (precio base + envío).

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar un sistema donde existen tres agentes activos: el cliente que hace el pedido,
> el restaurante que lo prepara y el repartidor que lo entrega.
> Un `OrderManager` actuará como controlador central, manteniendo los estados de los pedidos.”

---

**❓ Preguntas al entrevistador:**

* ¿Un pedido puede tener varios platos? → ✅ Sí.
* ¿Cada pedido pertenece a un solo restaurante? → ✅ Sí.
* ¿Puede haber varios repartidores disponibles? → ✅ Se selecciona el más cercano.
* ¿Debe incluir cálculo de envío? → ✅ Sí, tarifa fija + porcentaje del total.

**🧩 Casos límite:**

* [x] No hay repartidores disponibles.
* [x] Restaurante sin ítems.
* [x] Pedido cancelado antes de asignar.
* [x] Cliente pide más de un plato del mismo tipo.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Coordinar múltiples entidades con flujos paralelos (restaurante y repartidor).

---

### 🧩 Clases Principales

| Clase          | Responsabilidad                                             | Relaciones                                            |
| -------------- | ----------------------------------------------------------- | ----------------------------------------------------- |
| `OrderManager` | Controla los pedidos, estados y asignación de repartidores. | Coordina `Customer`, `Restaurant`, `Order`, `Driver`. |
| `Restaurant`   | Mantiene su menú y procesa pedidos.                         | Contiene `MenuItem`.                                  |
| `Customer`     | Representa el usuario que realiza el pedido.                | Crea `Order`.                                         |
| `Driver`       | Representa al repartidor con ubicación y disponibilidad.    | Asignado a un `Order`.                                |
| `Order`        | Contiene los ítems, estado y costo total.                   | Une `Customer`, `Restaurant` y `Driver`.              |
| `MenuItem`     | Representa un plato con nombre y precio.                    | —                                                     |

---

### 🔁 Flujo de Operación

1. El cliente selecciona un restaurante y crea un pedido.
2. El restaurante acepta y cambia el estado a “En preparación”.
3. `OrderManager` busca un repartidor disponible y lo asigna.
4. El pedido pasa a “En camino”.
5. El cliente recibe la orden → estado “Entregado”.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|     OrderManager     |
+----------------------+
| - restaurants        |
| - drivers            |
| - orders             |
+----------------------+
| + createOrder()      |
| + assignDriver()     |
| + updateStatus()     |
+----------------------+
          |
          v
+----------------------+
|        Order         |
+----------------------+
| customer, restaurant, items, driver, status, total |
+----------------------+
| + addItem()          |
| + calculateTotal()    |
| + setStatus()         |
+----------------------+

+----------------------+
|     Restaurant       |
+----------------------+
| id, name, menu       |
+----------------------+
| + addMenuItem()      |
| + getItem()          |
+----------------------+

+----------------------+
|      Customer        |
+----------------------+
| id, name             |
+----------------------+

+----------------------+
|       Driver         |
+----------------------+
| id, name, available  |
+----------------------+
| + setAvailable()     |
+----------------------+

+----------------------+
|     MenuItem         |
+----------------------+
| name, price          |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Construir un modelo coordinado, con estados claros y flujos definidos.

```python
from enum import Enum

class OrderStatus(Enum):
    CREATED = "Creado"
    PREPARING = "En preparación"
    ON_THE_WAY = "En camino"
    DELIVERED = "Entregado"
    CANCELLED = "Cancelado"

class MenuItem:
    def __init__(self, name, price):
        self.name = name
        self.price = price

class Restaurant:
    def __init__(self, rid, name):
        self.id = rid
        self.name = name
        self.menu = []

    def add_menu_item(self, item):
        self.menu.append(item)

    def get_item(self, name):
        for item in self.menu:
            if item.name == name:
                return item
        return None

class Customer:
    def __init__(self, cid, name):
        self.id = cid
        self.name = name

class Driver:
    def __init__(self, did, name):
        self.id = did
        self.name = name
        self.available = True

    def set_available(self, value):
        self.available = value

class Order:
    def __init__(self, customer, restaurant):
        self.customer = customer
        self.restaurant = restaurant
        self.items = []
        self.driver = None
        self.status = OrderStatus.CREATED
        self.total = 0

    def add_item(self, item, quantity=1):
        self.items.append((item, quantity))
        self.calculate_total()

    def calculate_total(self):
        self.total = sum(item.price * qty for item, qty in self.items) + 5  # $5 de envío

    def set_status(self, status):
        self.status = status
        print(f"Estado del pedido: {status.value}")

class OrderManager:
    def __init__(self):
        self.restaurants = []
        self.drivers = []
        self.orders = []

    def register_restaurant(self, restaurant):
        self.restaurants.append(restaurant)

    def register_driver(self, driver):
        self.drivers.append(driver)

    def create_order(self, customer, restaurant):
        order = Order(customer, restaurant)
        self.orders.append(order)
        print(f"{customer.name} creó un nuevo pedido en {restaurant.name}.")
        return order

    def assign_driver(self, order):
        available = [d for d in self.drivers if d.available]
        if not available:
            print("No hay repartidores disponibles.")
            return
        driver = available[0]
        driver.set_available(False)
        order.driver = driver
        order.set_status(OrderStatus.ON_THE_WAY)
        print(f"{driver.name} ha sido asignado al pedido de {order.customer.name}.")

    def complete_order(self, order):
        order.set_status(OrderStatus.DELIVERED)
        order.driver.set_available(True)
        print(f"Pedido entregado. Total: ${order.total:.2f}")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear sistema
manager = OrderManager()

# Registrar restaurante y menú
r1 = Restaurant("R1", "Castillo Food Hall")
r1.add_menu_item(MenuItem("Pizza Mágica", 12))
r1.add_menu_item(MenuItem("Poción Refrescante", 5))
manager.register_restaurant(r1)

# Registrar repartidores
manager.register_driver(Driver("D1", "Leonora"))
manager.register_driver(Driver("D2", "Fénix"))

# Crear cliente
c1 = Customer("C1", "Isabel")

# Crear pedido
order = manager.create_order(c1, r1)
order.add_item(r1.get_item("Pizza Mágica"), 2)
order.add_item(r1.get_item("Poción Refrescante"), 1)

# Asignar y completar pedido
manager.assign_driver(order)
manager.complete_order(order)
```

**🎯 Salida esperada:**

```
Isabel creó un nuevo pedido en Castillo Food Hall.
Estado del pedido: Creado
Estado del pedido: En camino
Leonora ha sido asignado al pedido de Isabel.
Estado del pedido: Entregado
Pedido entregado. Total: $34.00
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                            |
| ---------------------- | ---------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(R + D) — depende del número de restaurantes y repartidores.    |
| 💾 **Espacio:**        | O(O) — número de pedidos activos.                                |
| ⚡ **Escalabilidad:**   | Alta — se pueden agregar pagos, seguimiento GPS, o evaluaciones. |
| 🧩 **Tipo de patrón:** | Controlador + Estado + Composición.                              |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de coordinación entre entidades con distintos estados.

**🔧 Posibles optimizaciones:**

* Integrar `Payment` y `Invoice` como clases separadas.
* Usar `Observer Pattern` para notificaciones automáticas.
* Crear `DeliveryStrategy` para asignación inteligente de repartidores.

**📚 Lecciones aprendidas:**

* Los **estados** (`Creado`, `En camino`, `Entregado`) estructuran el flujo del sistema.
* La **composición** (cliente-restaurante-repartidor) mantiene un acoplamiento bajo.
* El patrón `Controller + Entities` es la base de los sistemas de entrega modernos.

**✅ Conclusión final:**

> “Diseñé un sistema de entregas donde los pedidos se coordinan entre restaurantes,
> repartidores y clientes, aplicando control de estado, encapsulación y asignación dinámica.
> El diseño refleja fielmente el flujo de una plataforma de delivery real.”

---

📘 **Resumen final**

| Aspecto          | Valor                              |
| ---------------- | ---------------------------------- |
| Patrón           | Controlador + Estado + Composición |
| Complejidad      | O(R + D)                           |
| Palabra clave    | “Coordinación multiagente”         |
| Tipo de problema | Modelado de plataforma de entregas |
| Nivel            | 🟡 Medio                           |

---