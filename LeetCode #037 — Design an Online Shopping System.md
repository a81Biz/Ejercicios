# 🛒 **LeetCode #037 — Design an Online Shopping System**

> **Tema:** Gestión de productos, carritos y órdenes con validación de stock
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Entidades transaccionales*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que modele una **tienda en línea**,
> con usuarios, productos, carritos, órdenes y pagos, aplicando reglas de stock y estados de compra.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **usuarios** y **productos** con precios y cantidades.
* Permitir a los usuarios **agregar productos a un carrito de compras**.
* Verificar la **disponibilidad de stock** antes de procesar un pedido.
* Crear una **orden** con los productos seleccionados.
* Calcular el **total** y registrar el **pago**.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar una tienda en línea que separa las entidades principales:
> `Product`, `User`, `Cart`, `Order` y `Payment`.
> El `ShopManager` coordina todas las operaciones: agregar productos, crear carritos,
> validar stock y registrar órdenes pagadas.”

---

**❓ Preguntas al entrevistador:**

* ¿Un usuario puede tener varios pedidos? → ✅ Sí.
* ¿Debe manejarse stock por producto? → ✅ Sí.
* ¿El carrito se vacía tras confirmar la orden? → ✅ Correcto.
* ¿Debe manejarse estado del pago? → ✅ Sí, básico (Pagado o Pendiente).

**🧩 Casos límite:**

* [x] Intentar comprar más unidades que las disponibles.
* [x] Carrito vacío.
* [x] Pago duplicado.
* [x] Stock agotado tras otra orden.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Mantener separación entre capa de inventario, carrito y órdenes,
> garantizando integridad de stock y trazabilidad de pagos.

---

### 🧩 Clases Principales

| Clase         | Responsabilidad                                      | Relaciones                         |
| ------------- | ---------------------------------------------------- | ---------------------------------- |
| `ShopManager` | Controla usuarios, productos, carritos y órdenes.    | Coordina todo el flujo.            |
| `User`        | Representa al comprador.                             | Posee `Cart` y `Orders`.           |
| `Product`     | Representa un artículo con precio y stock.           | Usado en `CartItem` y `OrderItem`. |
| `Cart`        | Contiene los productos seleccionados antes del pago. | Pertenece a un `User`.             |
| `Order`       | Representa una compra confirmada.                    | Contiene `OrderItem` y `Payment`.  |
| `Payment`     | Registra el estado de pago de una orden.             | Asociado a `Order`.                |

---

### 🔁 Flujo de Operación

1. Se registran productos y usuarios.
2. El usuario agrega productos al carrito.
3. El sistema verifica disponibilidad.
4. Si hay stock, se crea una orden.
5. El pago se procesa → el carrito se vacía.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|     ShopManager      |
+----------------------+
| - users              |
| - products           |
| - orders             |
+----------------------+
| + registerUser()     |
| + addProduct()       |
| + addToCart()        |
| + checkout()         |
+----------------------+
          |
          v
+----------------------+
|        User          |
+----------------------+
| id, name, cart, orders |
+----------------------+

+----------------------+
|       Product        |
+----------------------+
| id, name, price, stock |
+----------------------+

+----------------------+
|        Cart          |
+----------------------+
| items                |
+----------------------+

+----------------------+
|       Order          |
+----------------------+
| user, items, total, payment |
+----------------------+

+----------------------+
|      Payment         |
+----------------------+
| amount, status       |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar flujo completo desde agregar productos hasta procesar el pago.

```python
class Product:
    def __init__(self, pid, name, price, stock):
        self.id = pid
        self.name = name
        self.price = price
        self.stock = stock

    def reduce_stock(self, quantity):
        if self.stock < quantity:
            raise ValueError(f"Stock insuficiente para {self.name}")
        self.stock -= quantity

class CartItem:
    def __init__(self, product, quantity):
        self.product = product
        self.quantity = quantity

class Cart:
    def __init__(self):
        self.items = []

    def add_item(self, product, quantity):
        for item in self.items:
            if item.product == product:
                item.quantity += quantity
                return
        self.items.append(CartItem(product, quantity))

    def calculate_total(self):
        return sum(item.product.price * item.quantity for item in self.items)

    def clear(self):
        self.items.clear()

class Payment:
    def __init__(self, amount):
        self.amount = amount
        self.status = "Pendiente"

    def process(self):
        self.status = "Pagado"
        print(f"💳 Pago procesado por ${self.amount:.2f}")

class OrderItem:
    def __init__(self, product, quantity):
        self.product = product
        self.quantity = quantity
        self.subtotal = product.price * quantity

class Order:
    def __init__(self, user, cart_items):
        self.user = user
        self.items = [OrderItem(i.product, i.quantity) for i in cart_items]
        self.total = sum(i.subtotal for i in self.items)
        self.payment = Payment(self.total)
        self.status = "Creada"

    def confirm_payment(self):
        self.payment.process()
        self.status = "Pagada"
        print(f"🧾 Orden de {self.user.name} confirmada. Total: ${self.total:.2f}")

class User:
    def __init__(self, uid, name):
        self.id = uid
        self.name = name
        self.cart = Cart()
        self.orders = []

class ShopManager:
    def __init__(self):
        self.users = []
        self.products = []
        self.orders = []

    def register_user(self, uid, name):
        user = User(uid, name)
        self.users.append(user)
        print(f"🧍 Usuario {name} registrado.")
        return user

    def add_product(self, pid, name, price, stock):
        p = Product(pid, name, price, stock)
        self.products.append(p)
        print(f"📦 Producto agregado: {name} (${price}, Stock: {stock})")
        return p

    def add_to_cart(self, user, product, quantity):
        if product.stock < quantity:
            print(f"🚫 Stock insuficiente para {product.name}.")
            return
        user.cart.add_item(product, quantity)
        print(f"🛒 {quantity}x {product.name} agregado al carrito de {user.name}.")

    def checkout(self, user):
        if not user.cart.items:
            print("⚠️ Carrito vacío.")
            return
        try:
            for item in user.cart.items:
                item.product.reduce_stock(item.quantity)
            order = Order(user, user.cart.items)
            order.confirm_payment()
            self.orders.append(order)
            user.orders.append(order)
            user.cart.clear()
        except ValueError as e:
            print(f"🚫 Error en checkout: {e}")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear tienda
shop = ShopManager()

# Registrar usuarios y productos
u1 = shop.register_user("U1", "Isabel")
p1 = shop.add_product("P1", "Dado de Hierro", 10, 5)
p2 = shop.add_product("P2", "Miniatura de Dragón", 25, 2)

# Agregar productos al carrito
shop.add_to_cart(u1, p1, 3)
shop.add_to_cart(u1, p2, 1)

# Procesar compra
shop.checkout(u1)

# Caso: stock insuficiente
shop.add_to_cart(u1, p2, 3)
shop.checkout(u1)
```

**🎯 Salida esperada:**

```
🧍 Usuario Isabel registrado.
📦 Producto agregado: Dado de Hierro ($10, Stock: 5)
📦 Producto agregado: Miniatura de Dragón ($25, Stock: 2)
🛒 3x Dado de Hierro agregado al carrito de Isabel.
🛒 1x Miniatura de Dragón agregado al carrito de Isabel.
💳 Pago procesado por $55.00
🧾 Orden de Isabel confirmada. Total: $55.00
🚫 Stock insuficiente para Miniatura de Dragón.
⚠️ Carrito vacío.
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                            |
| ---------------------- | -------------------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(P + C) — productos y elementos del carrito.                                    |
| 💾 **Espacio:**        | O(U + O + P) — usuarios, órdenes y productos.                                    |
| ⚡ **Escalabilidad:**   | Alta — se puede extender con catálogos, cupones, o integración con APIs de pago. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Validación transaccional.                            |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Demostrar dominio del flujo de comercio electrónico con integridad de inventario.

**🔧 Posibles optimizaciones:**

* Clase `InventoryManager` para manejo centralizado de stock.
* Integrar `Discount` y `Coupon`.
* Agregar manejo de `OrderStatus` (Creada, Pagada, Enviada, Entregada).

**📚 Lecciones aprendidas:**

* La **composición** entre carrito, órdenes y productos facilita la trazabilidad.
* El **control de stock** debe ejecutarse justo antes del pago.
* El modelo es base para sistemas e-commerce modulares (WooCommerce, Shopify, etc.).

**✅ Conclusión final:**

> “Diseñé un sistema de tienda en línea con flujo completo: carrito, validación de stock,
> creación de órdenes y procesamiento de pagos.
> El modelo aplica principios de integridad, encapsulación y extensibilidad.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | Controlador + Composición + Validación |
| Complejidad      | O(P + C)                               |
| Palabra clave    | “Transacciones de compra seguras”      |
| Tipo de problema | Modelado de tienda en línea            |
| Nivel            | 🟡 Medio                               |

---