# 🥤 **LeetCode #026 — Design a Basic Vending Machine**

> **Tema:** Principios OOP fundamentales — encapsulación, estado y responsabilidad única
> **Nivel:** 🟢 Fácil
> **Patrón:** *Encapsulación + Composición de objetos*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar una máquina expendedora capaz de aceptar monedas, vender productos y devolver cambio.
> Debe estructurarse en clases coherentes que separen la lógica de inventario, transacciones y manejo de dinero.

---

**📖 Enunciado resumido:**
Diseñar un sistema orientado a objetos para una **vending machine** que:

* Permita insertar dinero.
* Muestre productos disponibles.
* Entregue producto si hay saldo suficiente.
* Devuelva cambio cuando corresponda.
* Permita modo mantenimiento para recargar stock o monedas.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar una máquina compuesta por subsistemas (inventario, monedas, flujo de compra).
> Cada clase tendrá una responsabilidad única y un estado interno controlado.”

---

**❓ Preguntas al entrevistador:**

* ¿Debe manejar múltiples denominaciones? → ✅ Sí.
* ¿Qué pasa si no hay cambio suficiente? → ❌ No se completa la compra.
* ¿Habrá modo mantenimiento? → ✅ Sí, para reposición.
* ¿Debe implementarse interfaz visual? → 💬 No, solo modelado OOP lógico.

**🧩 Casos límite:**

* [x] Producto sin stock.
* [x] Saldo insuficiente.
* [x] Cambio exacto.
* [x] Varias monedas insertadas.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Dividir responsabilidades en clases pequeñas, independientes y cohesivas.

---

### 🧩 Clases Principales

| Clase            | Responsabilidad                                  | Relaciones                        |
| ---------------- | ------------------------------------------------ | --------------------------------- |
| `VendingMachine` | Controla el flujo principal de la operación.     | Usa `Inventory`, `CoinDispenser`. |
| `Inventory`      | Mantiene el stock de productos.                  | Contiene `ProductSlot`.           |
| `ProductSlot`    | Representa el producto y su cantidad disponible. | Contiene `Product`.               |
| `Product`        | Define un artículo (nombre, código, precio).     | —                                 |
| `CoinDispenser`  | Gestiona dinero insertado y cambio.              | —                                 |

---

### 🔁 Flujo de Operación

1. Usuario inserta moneda.
2. Usuario elige producto.
3. Máquina valida saldo y existencia.
4. Si el saldo es suficiente → entrega producto y devuelve cambio.
5. Si no → muestra error y saldo faltante.
6. Modo mantenimiento → permite recargar productos o monedas.

---

### 🧱 Relaciones UML (simplificadas)

```
+-------------------+
|  VendingMachine   |
+-------------------+
| - inventory       |
| - coinDispenser   |
+-------------------+
| + insertCoin()    |
| + selectProduct() |
| + dispense()      |
+-------------------+
          |
          v
+-------------------+
|     Inventory     |
+-------------------+
| + addProduct()    |
| + getProduct()    |
| + reduceStock()   |
+-------------------+

+-------------------+
|     Product       |
+-------------------+
| code, name, price |
+-------------------+

+-------------------+
|  CoinDispenser    |
+-------------------+
| + insertCoin()    |
| + getChange()     |
+-------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Mostrar un modelo funcional simple y modular.

```python
class Product:
    def __init__(self, code, name, price):
        self.code = code
        self.name = name
        self.price = price


class ProductSlot:
    def __init__(self, product, quantity):
        self.product = product
        self.quantity = quantity


class Inventory:
    def __init__(self):
        self.slots = {}

    def add_product(self, product, qty):
        self.slots[product.code] = ProductSlot(product, qty)

    def get_product(self, code):
        slot = self.slots.get(code)
        return slot.product if slot and slot.quantity > 0 else None

    def reduce_stock(self, code):
        if code in self.slots and self.slots[code].quantity > 0:
            self.slots[code].quantity -= 1


class CoinDispenser:
    def __init__(self):
        self.balance = 0

    def insert_coin(self, value):
        self.balance += value

    def deduct(self, amount):
        self.balance -= amount

    def get_balance(self):
        return self.balance


class VendingMachine:
    def __init__(self):
        self.inventory = Inventory()
        self.coins = CoinDispenser()

    def insert_coin(self, amount):
        self.coins.insert_coin(amount)
        print(f"Balance actual: ${self.coins.get_balance()}")

    def select_product(self, code):
        product = self.inventory.get_product(code)
        if not product:
            print("Producto no disponible.")
            return

        if self.coins.get_balance() < product.price:
            faltante = product.price - self.coins.get_balance()
            print(f"Saldo insuficiente. Faltan ${faltante}")
            return

        self.coins.deduct(product.price)
        self.inventory.reduce_stock(code)
        print(f"Entregado: {product.name}")

        if self.coins.get_balance() > 0:
            print(f"Tu cambio: ${self.coins.get_balance()}")
            self.coins.balance = 0
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Lógica

```python
# Configuración de la máquina
vm = VendingMachine()
vm.inventory.add_product(Product("A1", "Coca-Cola", 20), 3)
vm.inventory.add_product(Product("B2", "Papas", 15), 2)

# Flujo de usuario
vm.insert_coin(10)
vm.select_product("A1")   # Faltan $10
vm.insert_coin(20)
vm.select_product("A1")   # Entregado, cambio $10
```

**🎯 Salida esperada:**

```
Balance actual: $10
Saldo insuficiente. Faltan $10
Balance actual: $30
Entregado: Coca-Cola
Tu cambio: $10
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                    |
| ---------------------- | ------------------------------------------------------------------------ |
| ⏱️ **Tiempo:**         | O(1) — operaciones simples de consulta y actualización.                  |
| 💾 **Espacio:**        | O(N) — depende del número de productos registrados.                      |
| ⚡ **Escalabilidad:**   | Alta — se pueden agregar estrategias de pago o nuevos tipos de producto. |
| 🧩 **Tipo de patrón:** | Encapsulación + Composición.                                             |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de encapsulación, composición y manejo de estado.

**🔧 Posibles optimizaciones:**

* Implementar una clase `PaymentStrategy` (efectivo, tarjeta, app).
* Añadir manejo de errores (`try/except`).
* Integrar logs de auditoría o telemetría.

**📚 Lecciones aprendidas:**

* La **composición** favorece flexibilidad sobre la herencia.
* La **responsabilidad única** mejora legibilidad y mantenimiento.
* El **estado interno** debe ser controlado de forma explícita.

**✅ Conclusión final:**

> “Diseñé una máquina expendedora aplicando principios OOP fundamentales.
> Cada clase tiene una responsabilidad única y puede extenderse sin romper el sistema.
> El modelo demuestra encapsulación efectiva y separación de preocupaciones.”

---

📘 **Resumen final**

| Aspecto          | Valor                       |
| ---------------- | --------------------------- |
| Patrón           | Encapsulación + Composición |
| Complejidad      | O(1)                        |
| Palabra clave    | “Responsabilidad única”     |
| Tipo de problema | Modelado OOP básico         |
| Nivel            | 🟢 Fácil                    |

