# 🏥 **LeetCode #042 — Design a Hospital Management System**

> **Tema:** Pacientes, doctores, citas, tratamientos y estados clínicos dinámicos
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Relaciones N:M con estados*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que modele un **Hospital**,
> controlando **pacientes, doctores, citas médicas, diagnósticos y tratamientos**,
> con registro de **estados de atención (pendiente, en consulta, dado de alta)** y facturación.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **doctores** y **pacientes**.
* Programar **citas médicas** entre ambos.
* Asignar **diagnósticos y tratamientos**.
* Cambiar el **estado de la cita** según su progreso.
* Registrar **facturas** (consultas y tratamientos).
* Permitir consultar **historial clínico** de cada paciente.

---

**💬 Reexplicación en voz alta:**

> “Voy a diseñar un sistema hospitalario donde pacientes y doctores pueden tener múltiples citas.
> Cada cita tiene un estado dinámico y puede generar un diagnóstico, tratamiento y factura.
> El `HospitalManager` será el controlador principal que coordina doctores, pacientes y citas.”

---

**❓ Preguntas al entrevistador:**

* ¿Cada paciente puede tener varios doctores? → ✅ Sí.
* ¿Las citas pueden reagendarse? → 🟡 En esta versión no, pero se puede extender.
* ¿El tratamiento tiene costo? → ✅ Sí, suma al total de la factura.
* ¿Se requiere registro histórico? → ✅ Sí, por paciente.

**🧩 Casos límite:**

* [x] Intentar agendar con un doctor o paciente inexistente.
* [x] Cambiar estado de una cita no iniciada.
* [x] Aplicar tratamiento sin diagnóstico previo.
* [x] Generar factura sin servicios registrados.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Estructurar entidades médicas y administrativas con relaciones N:M y estados controlados.

---

### 🧩 Clases Principales

| Clase             | Responsabilidad                                  | Relaciones                                   |
| ----------------- | ------------------------------------------------ | -------------------------------------------- |
| `HospitalManager` | Coordina doctores, pacientes y citas.            | Gestiona todo el flujo médico.               |
| `Doctor`          | Representa a un médico especialista.             | Tiene múltiples pacientes y citas.           |
| `Patient`         | Representa a un paciente.                        | Posee historial médico.                      |
| `Appointment`     | Cita médica entre paciente y doctor.             | Contiene diagnóstico, tratamiento y factura. |
| `Diagnosis`       | Representa el diagnóstico emitido por el doctor. | Asociado a `Appointment`.                    |
| `Treatment`       | Detalla el tratamiento aplicado y su costo.      | Asociado a `Diagnosis` o `Appointment`.      |
| `Invoice`         | Representa la facturación de la cita.            | Asociada a `Appointment`.                    |

---

### 🔁 Flujo de Operación

1. Registrar doctores y pacientes.
2. Agendar cita entre ambos con fecha y hora.
3. Cambiar estado de la cita a *En Consulta*.
4. Registrar diagnóstico y tratamiento.
5. Generar factura total.
6. Cambiar estado a *Dado de Alta*.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|   HospitalManager    |
+----------------------+
| - doctors, patients, appointments |
+----------------------+
| + registerDoctor()   |
| + registerPatient()  |
| + scheduleAppointment() |
| + diagnose()         |
| + treat()            |
| + discharge()        |
| + generateInvoice()  |
+----------------------+
          |
          v
+----------------------+
|     Appointment      |
+----------------------+
| doctor, patient, date, status, diagnosis, treatment, invoice |
+----------------------+

+----------------------+
|      Doctor          |
+----------------------+
| id, name, specialty  |
+----------------------+

+----------------------+
|      Patient         |
+----------------------+
| id, name, history    |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Controlar ciclo completo de atención médica: cita → diagnóstico → tratamiento → factura → alta.

```python
from datetime import datetime

class AppointmentStatus:
    SCHEDULED = "Programada"
    IN_PROGRESS = "En Consulta"
    COMPLETED = "Dado de Alta"

class Doctor:
    def __init__(self, did, name, specialty):
        self.id = did
        self.name = name
        self.specialty = specialty
        self.appointments = []

    def __str__(self):
        return f"👨‍⚕️ Dr. {self.name} ({self.specialty})"

class Patient:
    def __init__(self, pid, name):
        self.id = pid
        self.name = name
        self.history = []
        self.appointments = []

    def __str__(self):
        return f"🧍 Paciente: {self.name}"

class Diagnosis:
    def __init__(self, description):
        self.description = description
        self.date = datetime.now()

class Treatment:
    def __init__(self, description, cost):
        self.description = description
        self.cost = cost
        self.date = datetime.now()

class Invoice:
    def __init__(self):
        self.items = []
        self.total = 0.0

    def add_item(self, desc, cost):
        self.items.append((desc, cost))
        self.total += cost

    def show(self):
        print("🧾 Factura:")
        for desc, cost in self.items:
            print(f"  - {desc}: ${cost:.2f}")
        print(f"💰 Total: ${self.total:.2f}\n")

class Appointment:
    def __init__(self, doctor, patient, date):
        self.doctor = doctor
        self.patient = patient
        self.date = date
        self.status = AppointmentStatus.SCHEDULED
        self.diagnosis = None
        self.treatment = None
        self.invoice = Invoice()

    def start(self):
        if self.status != AppointmentStatus.SCHEDULED:
            print("⚠️ Cita no puede iniciarse (ya en progreso o completada).")
            return
        self.status = AppointmentStatus.IN_PROGRESS
        print(f"🩺 Cita entre {self.patient.name} y {self.doctor.name} iniciada.")

    def diagnose(self, description):
        if self.status != AppointmentStatus.IN_PROGRESS:
            print("⚠️ No se puede diagnosticar sin iniciar la cita.")
            return
        self.diagnosis = Diagnosis(description)
        print(f"📋 Diagnóstico registrado: {description}")

    def treat(self, description, cost):
        if not self.diagnosis:
            print("⚠️ Debe existir un diagnóstico antes del tratamiento.")
            return
        self.treatment = Treatment(description, cost)
        self.invoice.add_item(description, cost)
        print(f"💊 Tratamiento aplicado: {description} (${cost:.2f})")

    def complete(self):
        if self.status != AppointmentStatus.IN_PROGRESS:
            print("⚠️ Cita no está en curso.")
            return
        self.status = AppointmentStatus.COMPLETED
        self.patient.history.append(self)
        print(f"✅ Cita completada y paciente {self.patient.name} dado de alta.")

class HospitalManager:
    def __init__(self):
        self.doctors = {}
        self.patients = {}
        self.appointments = []

    def register_doctor(self, did, name, specialty):
        if did in self.doctors:
            print("⚠️ Doctor ya existente.")
            return
        d = Doctor(did, name, specialty)
        self.doctors[did] = d
        print(f"👨‍⚕️ Doctor registrado: {d}")
        return d

    def register_patient(self, pid, name):
        if pid in self.patients:
            print("⚠️ Paciente ya existente.")
            return
        p = Patient(pid, name)
        self.patients[pid] = p
        print(f"🧍 Paciente registrado: {p}")
        return p

    def schedule_appointment(self, did, pid, date):
        doctor = self.doctors.get(did)
        patient = self.patients.get(pid)
        if not doctor or not patient:
            print("🚫 Doctor o paciente no encontrado.")
            return
        appt = Appointment(doctor, patient, date)
        doctor.appointments.append(appt)
        patient.appointments.append(appt)
        self.appointments.append(appt)
        print(f"📅 Cita programada: {doctor.name} con {patient.name} el {date.strftime('%Y-%m-%d')}")
        return appt

    def generate_invoice(self, appointment):
        if not appointment.invoice.items:
            print("⚠️ No hay servicios registrados para facturar.")
            return
        appointment.invoice.show()
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear hospital
hospital = HospitalManager()

# Registrar doctor y pacientes
d1 = hospital.register_doctor("D1", "Carlos Rivera", "Cardiología")
p1 = hospital.register_patient("P1", "Isabel Martínez")

# Agendar cita
appt = hospital.schedule_appointment("D1", "P1", datetime(2025, 10, 10))

# Flujo de atención médica
appt.start()
appt.diagnose("Arritmia leve")
appt.treat("Medicación beta bloqueante", 1200)
hospital.generate_invoice(appt)
appt.complete()
```

**🎯 Salida esperada:**

```
👨‍⚕️ Doctor registrado: 👨‍⚕️ Dr. Carlos Rivera (Cardiología)
🧍 Paciente registrado: 🧍 Paciente: Isabel Martínez
📅 Cita programada: Carlos Rivera con Isabel Martínez el 2025-10-10
🩺 Cita entre Isabel Martínez y Carlos Rivera iniciada.
📋 Diagnóstico registrado: Arritmia leve
💊 Tratamiento aplicado: Medicación beta bloqueante ($1200.00)
🧾 Factura:
  - Medicación beta bloqueante: $1200.00
💰 Total: $1200.00
✅ Cita completada y paciente Isabel Martínez dado de alta.
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                     |
| ---------------------- | ------------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(D + P + A) — doctores, pacientes y citas.                               |
| 💾 **Espacio:**        | O(D + P + H) — doctores, pacientes e historial.                           |
| ⚡ **Escalabilidad:**   | Alta — se puede extender a hospital con múltiples especialidades o sedes. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Estado dinámico.                              |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Demostrar control del flujo de atención médica y facturación modular.

**🔧 Posibles optimizaciones:**

* Añadir `Department` (departamento hospitalario).
* Integrar `BillingSystem` con impuestos o seguros.
* Añadir módulo de **reagendamiento y cancelación** de citas.

**📚 Lecciones aprendidas:**

* Las **relaciones N:M** entre doctores y pacientes requieren control centralizado.
* La **transición de estados** garantiza consistencia clínica.
* Este modelo es adaptable a hospitales reales o clínicas privadas.

**✅ Conclusión final:**

> “Diseñé un sistema hospitalario modular que gestiona doctores, pacientes, citas, diagnósticos y facturación,
> aplicando composición y control de estados.
> El modelo refleja el flujo real de atención médica moderna.”

---

📘 **Resumen final**

| Aspecto          | Valor                                       |
| ---------------- | ------------------------------------------- |
| Patrón           | Controlador + Composición + Estado dinámico |
| Complejidad      | O(D + P + A)                                |
| Palabra clave    | “Gestión clínica y facturación”             |
| Tipo de problema | Modelado de sistema hospitalario            |
| Nivel            | 🟡 Medio                                    |

---