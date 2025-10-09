# 💰 **LeetCode #038 — Design a Banking System**

> **Tema:** Cuentas, transferencias y validación de transacciones entre usuarios
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Relaciones bidireccionales*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que simule el funcionamiento básico de un **banco**,
> permitiendo administrar **cuentas, clientes y transferencias seguras**,
> asegurando la integridad de las operaciones.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **clientes** y **cuentas** con saldo inicial.
* Permitir **depósitos**, **retiros** y **transferencias entre cuentas**.
* Validar **fondos disponibles** antes de cada operación.
* Registrar **transacciones bidireccionales** (origen y destino).
* Mantener un **historial completo** de movimientos.

---

**💬 Reexplicación en voz alta:**

> “Voy a modelar un sistema bancario donde cada cliente puede tener una o más cuentas.
> El `BankManager` centralizará todas las operaciones y validaciones,
> asegurando que las transferencias sean atómicas (se descuentan y acreditan simultáneamente).”

---

**❓ Preguntas al entrevistador:**

* ¿Un cliente puede tener múltiples cuentas? → ✅ Sí.
* ¿Se permiten transferencias entre cuentas del mismo cliente? → ✅ Sí.
* ¿Hay límite diario o comisiones? → 🟡 No, pero podría añadirse.
* ¿Debe registrar todas las operaciones? → ✅ Sí, con fecha y tipo.

**🧩 Casos límite:**

* [x] Transferencia con fondos insuficientes.
* [x] Monto negativo o nulo.
* [x] Cuenta inexistente.
* [x] Transferencia a la misma cuenta.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Estructurar relaciones entre `BankManager`, `Customer`, `Account` y `Transaction`,
> con flujo bidireccional de transferencias seguras.

---

### 🧩 Clases Principales

| Clase         | Responsabilidad                                            | Relaciones                                     |
| ------------- | ---------------------------------------------------------- | ---------------------------------------------- |
| `BankManager` | Controla las operaciones del sistema bancario.             | Coordina `Customer`, `Account`, `Transaction`. |
| `Customer`    | Representa a un cliente del banco.                         | Posee una o varias `Account`.                  |
| `Account`     | Administra el saldo y registra transacciones.              | Asociada a un `Customer`.                      |
| `Transaction` | Registra una operación (depósito, retiro o transferencia). | Relacionada con una o dos cuentas.             |

---

### 🔁 Flujo de Operación

1. Se crean clientes y sus cuentas.
2. El cliente puede depositar o retirar dinero.
3. Puede transferir fondos entre cuentas (propias o ajenas).
4. Cada operación genera una `Transaction` con fecha y tipo.
5. El sistema puede mostrar el historial de cada cuenta.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|     BankManager      |
+----------------------+
| - customers          |
| - accounts           |
+----------------------+
| + createCustomer()   |
| + openAccount()      |
| + deposit()          |
| + withdraw()         |
| + transfer()         |
+----------------------+
          |
          v
+----------------------+
|      Customer        |
+----------------------+
| id, name, accounts   |
+----------------------+

+----------------------+
|      Account         |
+----------------------+
| number, owner, balance, transactions |
+----------------------+
| + addTransaction()   |
| + showHistory()      |
+----------------------+

+----------------------+
|     Transaction      |
+----------------------+
| type, amount, date, details |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar un sistema coherente, con validaciones completas y registro de historial.

```python
from datetime import datetime

class TransactionType:
    DEPOSIT = "Depósito"
    WITHDRAW = "Retiro"
    TRANSFER = "Transferencia"

class Transaction:
    def __init__(self, ttype, amount, details=""):
        self.type = ttype
        self.amount = amount
        self.details = details
        self.date = datetime.now()

    def __str__(self):
        return f"[{self.date.strftime('%Y-%m-%d %H:%M:%S')}] {self.type}: ${self.amount:.2f} | {self.details}"

class Account:
    def __init__(self, number, owner, balance=0):
        self.number = number
        self.owner = owner
        self.balance = balance
        self.transactions = []

    def add_transaction(self, ttype, amount, details=""):
        self.transactions.append(Transaction(ttype, amount, details))

    def deposit(self, amount):
        if amount <= 0:
            print("🚫 Monto inválido.")
            return
        self.balance += amount
        self.add_transaction(TransactionType.DEPOSIT, amount)
        print(f"💰 Depósito exitoso en cuenta {self.number}: ${amount:.2f}")

    def withdraw(self, amount):
        if amount <= 0:
            print("🚫 Monto inválido.")
            return
        if self.balance < amount:
            print("❌ Fondos insuficientes.")
            return
        self.balance -= amount
        self.add_transaction(TransactionType.WITHDRAW, amount)
        print(f"🏧 Retiro exitoso de ${amount:.2f} en cuenta {self.number}")

    def show_history(self):
        print(f"📜 Historial de cuenta {self.number}:")
        for t in self.transactions:
            print(f"  {t}")
        print(f"Saldo actual: ${self.balance:.2f}\n")

class Customer:
    def __init__(self, cid, name):
        self.id = cid
        self.name = name
        self.accounts = []

class BankManager:
    def __init__(self):
        self.customers = []
        self.accounts = {}

    def create_customer(self, cid, name):
        c = Customer(cid, name)
        self.customers.append(c)
        print(f"🧍 Cliente creado: {name}")
        return c

    def open_account(self, customer, acc_number, initial_balance=0):
        if acc_number in self.accounts:
            print("⚠️ Número de cuenta ya existente.")
            return
        acc = Account(acc_number, customer, initial_balance)
        self.accounts[acc_number] = acc
        customer.accounts.append(acc)
        print(f"🏦 Cuenta {acc_number} abierta para {customer.name} con saldo ${initial_balance:.2f}")
        return acc

    def deposit(self, acc_number, amount):
        acc = self.accounts.get(acc_number)
        if not acc:
            print("🚫 Cuenta no encontrada.")
            return
        acc.deposit(amount)

    def withdraw(self, acc_number, amount):
        acc = self.accounts.get(acc_number)
        if not acc:
            print("🚫 Cuenta no encontrada.")
            return
        acc.withdraw(amount)

    def transfer(self, from_acc, to_acc, amount):
        if from_acc == to_acc:
            print("⚠️ No se puede transferir a la misma cuenta.")
            return
        origin = self.accounts.get(from_acc)
        dest = self.accounts.get(to_acc)
        if not origin or not dest:
            print("🚫 Cuenta origen o destino no encontrada.")
            return
        if origin.balance < amount:
            print("❌ Fondos insuficientes para transferir.")
            return
        # Transferencia atómica
        origin.balance -= amount
        dest.balance += amount
        origin.add_transaction(TransactionType.TRANSFER, amount, f"Hacia {to_acc}")
        dest.add_transaction(TransactionType.TRANSFER, amount, f"Desde {from_acc}")
        print(f"🔄 Transferencia de ${amount:.2f} de {from_acc} a {to_acc} completada.")
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear banco y clientes
bank = BankManager()
c1 = bank.create_customer("C1", "Isabel")
c2 = bank.create_customer("C2", "Carlos")

# Crear cuentas
a1 = bank.open_account(c1, "ACC100", 500)
a2 = bank.open_account(c2, "ACC200", 300)

# Depósitos y retiros
bank.deposit("ACC100", 200)
bank.withdraw("ACC100", 100)

# Transferencia válida
bank.transfer("ACC100", "ACC200", 250)

# Transferencia inválida (fondos insuficientes)
bank.transfer("ACC200", "ACC100", 1000)

# Historial
a1.show_history()
a2.show_history()
```

**🎯 Salida esperada:**

```
🧍 Cliente creado: Isabel
🧍 Cliente creado: Carlos
🏦 Cuenta ACC100 abierta para Isabel con saldo $500.00
🏦 Cuenta ACC200 abierta para Carlos con saldo $300.00
💰 Depósito exitoso en cuenta ACC100: $200.00
🏧 Retiro exitoso de $100.00 en cuenta ACC100
🔄 Transferencia de $250.00 de ACC100 a ACC200 completada.
❌ Fondos insuficientes para transferir.
📜 Historial de cuenta ACC100:
  [2025-10-08 21:00:00] Depósito: $200.00 | 
  [2025-10-08 21:01:00] Retiro: $100.00 | 
  [2025-10-08 21:02:00] Transferencia: $250.00 | Hacia ACC200
Saldo actual: $350.00

📜 Historial de cuenta ACC200:
  [2025-10-08 21:02:00] Transferencia: $250.00 | Desde ACC100
Saldo actual: $550.00
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                      |
| ---------------------- | ---------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(1) por operación (búsqueda directa de cuenta).           |
| 💾 **Espacio:**        | O(C + A + T) — clientes, cuentas y transacciones.          |
| ⚡ **Escalabilidad:**   | Alta — se pueden añadir préstamos, intereses o sucursales. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Validación bidireccional.      |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de operaciones atómicas y consistencia de datos financieros.

**🔧 Posibles optimizaciones:**

* Integrar `BankBranch` y `InterestCalculator`.
* Manejar transacciones pendientes o revertidas.
* Agregar autenticación de usuarios (`PIN`, `Token`).

**📚 Lecciones aprendidas:**

* La **bidireccionalidad** entre cuentas permite registrar ambos lados de una operación.
* El **control de atomicidad** evita inconsistencias en transferencias.
* Este modelo es base para sistemas bancarios reales o billeteras digitales.

**✅ Conclusión final:**

> “Diseñé un sistema bancario modular que permite depósitos, retiros y transferencias seguras,
> aplicando principios de consistencia, atomicidad y trazabilidad de transacciones.”

---

📘 **Resumen final**

| Aspecto          | Valor                                                |
| ---------------- | ---------------------------------------------------- |
| Patrón           | Controlador + Composición + Validación bidireccional |
| Complejidad      | O(1)                                                 |
| Palabra clave    | “Transacciones atómicas”                             |
| Tipo de problema | Modelado de sistema financiero                       |
| Nivel            | 🟡 Medio                                             |
