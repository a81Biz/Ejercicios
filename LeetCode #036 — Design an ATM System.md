# 🏦 **LeetCode #036 — Design an ATM System**

> **Tema:** Modelado de transacciones seguras y control de estados financieros
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Validación de reglas de negocio*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que simule un **Cajero Automático (ATM)**,
> manejando **usuarios, cuentas, autenticación, retiros, depósitos y validaciones de saldo**.

---

**📖 Enunciado resumido:**
El sistema debe:

* Permitir **registrar cuentas** con saldo inicial.
* Validar **PIN o autenticación** antes de operar.
* Soportar **depósitos**, **retiros** y **consultas de saldo**.
* Impedir retiros que excedan el saldo.
* Registrar un **historial de transacciones**.

---

**💬 Reexplicación en voz alta:**

> “Voy a diseñar un sistema ATM que maneje cuentas de usuarios.
> El usuario se autentica con su número de cuenta y PIN,
> luego puede realizar operaciones dentro de los límites y con registro de movimientos.”

---

**❓ Preguntas al entrevistador:**

* ¿Debe manejar múltiples usuarios? → ✅ Sí.
* ¿Debe autenticar PIN en cada sesión? → ✅ Sí.
* ¿Existen límites de retiro por transacción? → 🟡 Sí, configurable.
* ¿Se debe almacenar historial? → ✅ Sí.

**🧩 Casos límite:**

* [x] PIN incorrecto.
* [x] Retiro mayor al saldo.
* [x] Monto negativo.
* [x] Sesión expirada o no iniciada.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Separar la lógica de seguridad, cuentas y operaciones en capas bien definidas.

---

### 🧩 Clases Principales

| Clase         | Responsabilidad                                             | Relaciones                          |
| ------------- | ----------------------------------------------------------- | ----------------------------------- |
| `ATMManager`  | Controla autenticación, sesiones y operaciones.             | Coordina `Account` y `Transaction`. |
| `Account`     | Representa la cuenta bancaria con PIN y saldo.              | Contiene `Transaction`.             |
| `Transaction` | Representa una operación (retiro, depósito, consulta).      | Asociada a `Account`.               |
| `ATM`         | Representa el dispositivo físico (opcional para expansión). | Usa `ATMManager`.                   |

---

### 🔁 Flujo de Operación

1. El usuario ingresa número de cuenta y PIN.
2. El sistema valida la autenticación.
3. El usuario selecciona operación: **Retiro**, **Depósito**, o **Consulta**.
4. El sistema ejecuta la transacción y actualiza el saldo.
5. Se guarda un registro de la operación.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|     ATMManager       |
+----------------------+
| - accounts           |
| - current_session    |
+----------------------+
| + authenticate()     |
| + deposit()          |
| + withdraw()         |
| + check_balance()    |
| + logout()           |
+----------------------+
          |
          v
+----------------------+
|       Account        |
+----------------------+
| account_no, pin, balance, history |
+----------------------+
| + add_transaction()  |
+----------------------+

+----------------------+
|     Transaction      |
+----------------------+
| type, amount, date   |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Crear un flujo completo con autenticación, control de sesión y registro de operaciones.

```python
from datetime import datetime

class TransactionType:
    DEPOSIT = "Depósito"
    WITHDRAW = "Retiro"
    BALANCE = "Consulta"

class Transaction:
    def __init__(self, ttype, amount, balance_after):
        self.type = ttype
        self.amount = amount
        self.date = datetime.now()
        self.balance_after = balance_after

    def __str__(self):
        return f"[{self.date.strftime('%Y-%m-%d %H:%M:%S')}] {self.type}: ${self.amount:.2f} | Saldo: ${self.balance_after:.2f}"

class Account:
    def __init__(self, account_no, pin, balance=0):
        self.account_no = account_no
        self.pin = pin
        self.balance = balance
        self.history = []

    def add_transaction(self, ttype, amount):
        self.history.append(Transaction(ttype, amount, self.balance))

class ATMManager:
    def __init__(self):
        self.accounts = {}
        self.current_session = None
        self.daily_withdraw_limit = 500  # límite diario de ejemplo

    def register_account(self, account_no, pin, balance=0):
        if account_no in self.accounts:
            print("⚠️ Cuenta ya existente.")
            return
        self.accounts[account_no] = Account(account_no, pin, balance)
        print(f"✅ Cuenta {account_no} creada con saldo ${balance:.2f}.")

    def authenticate(self, account_no, pin):
        account = self.accounts.get(account_no)
        if not account:
            print("🚫 Cuenta no encontrada.")
            return False
        if account.pin != pin:
            print("❌ PIN incorrecto.")
            return False
        self.current_session = account
        print(f"🔐 Sesión iniciada para cuenta {account_no}.")
        return True

    def check_session(self):
        if not self.current_session:
            print("⚠️ No hay sesión activa.")
            return False
        return True

    def deposit(self, amount):
        if not self.check_session(): return
        if amount <= 0:
            print("🚫 Monto inválido.")
            return
        self.current_session.balance += amount
        self.current_session.add_transaction(TransactionType.DEPOSIT, amount)
        print(f"💰 Depósito exitoso: ${amount:.2f}. Saldo actual: ${self.current_session.balance:.2f}")

    def withdraw(self, amount):
        if not self.check_session(): return
        if amount <= 0:
            print("🚫 Monto inválido.")
            return
        if amount > self.current_session.balance:
            print("❌ Fondos insuficientes.")
            return
        if amount > self.daily_withdraw_limit:
            print("⚠️ Excede el límite diario.")
            return
        self.current_session.balance -= amount
        self.current_session.add_transaction(TransactionType.WITHDRAW, amount)
        print(f"🏧 Retiro exitoso: ${amount:.2f}. Saldo restante: ${self.current_session.balance:.2f}")

    def check_balance(self):
        if not self.check_session(): return
        balance = self.current_session.balance
        self.current_session.add_transaction(TransactionType.BALANCE, 0)
        print(f"💼 Saldo actual: ${balance:.2f}")

    def show_history(self):
        if not self.check_session(): return
        print(f"📜 Historial de la cuenta {self.current_session.account_no}:")
        for t in self.current_session.history:
            print(f"  {t}")

    def logout(self):
        if self.current_session:
            print(f"🔒 Sesión cerrada ({self.current_session.account_no}).")
            self.current_session = None
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear sistema ATM
atm = ATMManager()

# Registrar cuentas
atm.register_account("001", "1234", 200)
atm.register_account("002", "9999", 1000)

# Autenticación y operaciones
atm.authenticate("001", "1234")
atm.deposit(150)
atm.withdraw(100)
atm.check_balance()
atm.show_history()
atm.logout()

# Caso: PIN incorrecto
atm.authenticate("002", "0000")

# Caso: Retiro excede saldo
atm.authenticate("002", "9999")
atm.withdraw(2000)
atm.logout()
```

**🎯 Salida esperada:**

```
✅ Cuenta 001 creada con saldo $200.00.
✅ Cuenta 002 creada con saldo $1000.00.
🔐 Sesión iniciada para cuenta 001.
💰 Depósito exitoso: $150.00. Saldo actual: $350.00
🏧 Retiro exitoso: $100.00. Saldo restante: $250.00
💼 Saldo actual: $250.00
📜 Historial de la cuenta 001:
  [2025-10-08 21:00:00] Depósito: $150.00 | Saldo: $350.00
  [2025-10-08 21:01:00] Retiro: $100.00 | Saldo: $250.00
  [2025-10-08 21:02:00] Consulta: $0.00 | Saldo: $250.00
🔒 Sesión cerrada (001).
❌ PIN incorrecto.
🔐 Sesión iniciada para cuenta 002.
❌ Fondos insuficientes.
🔒 Sesión cerrada (002).
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                              |
| ---------------------- | ---------------------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(1) por operación.                                                                |
| 💾 **Espacio:**        | O(N + T) — cuentas + transacciones.                                                |
| ⚡ **Escalabilidad:**   | Alta — se pueden añadir transferencias, tarjetas o límites diarios personalizados. |
| 🧩 **Tipo de patrón:** | Controlador + Validación + Composición.                                            |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de autenticación, transacciones y control de estados financieros.

**🔧 Posibles optimizaciones:**

* Añadir `TransactionID` y soporte de reversión.
* Agregar clase `Bank` para múltiples ATMs y sincronización de cuentas.
* Manejo de sesiones concurrentes o límites horarios.

**📚 Lecciones aprendidas:**

* La **composición** permite aislar lógica de cuentas y transacciones.
* El control de **autenticación** protege el flujo financiero.
* El sistema puede evolucionar hacia una **banca digital distribuida**.

**✅ Conclusión final:**

> “Diseñé un sistema ATM con autenticación, depósitos, retiros y registros de transacciones,
> aplicando validaciones y estructura modular.
> El diseño refleja una infraestructura financiera escalable y segura.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | Controlador + Composición + Validación |
| Complejidad      | O(1)                                   |
| Palabra clave    | “Transacciones seguras”                |
| Tipo de problema | Modelado de sistema bancario           |
| Nivel            | 🟡 Medio                               |

