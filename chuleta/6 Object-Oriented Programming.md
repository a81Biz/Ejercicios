## 6  Object-Oriented Programming (OOP) and SOLID Principles

---

### 6.1  Core Concepts of Object-Oriented Programming

Object-oriented programming models a system as a collection of **objects** that combine **state** (data) and **behavior** (methods).
The four classical pillars are:

| Concept           | Definition                                                                | Example                              |
| :---------------- | :------------------------------------------------------------------------ | :----------------------------------- |
| **Encapsulation** | Hiding internal details and exposing only what is necessary.              | Private fields, public methods.      |
| **Abstraction**   | Defining a simplified interface that hides complexity.                    | Interfaces or abstract classes.      |
| **Inheritance**   | Reusing and extending functionality of existing classes.                  | `class Manager : Employee`           |
| **Polymorphism**  | Treating objects of different types uniformly through a common interface. | Method overriding, virtual dispatch. |

#### Example

```csharp
public abstract class Employee
{
    public string Name { get; set; }
    public abstract decimal CalculatePay();
}

public class SalariedEmployee : Employee
{
    public decimal AnnualSalary { get; set; }
    public override decimal CalculatePay() => AnnualSalary / 12;
}

public class HourlyEmployee : Employee
{
    public decimal HourlyRate { get; set; }
    public int HoursWorked { get; set; }
    public override decimal CalculatePay() => HourlyRate * HoursWorked;
}
```

#### Senior Insight

Polymorphism is not limited to inheritance—**composition** often provides better flexibility.
Favor **interfaces over inheritance** to reduce coupling and increase testability.

---

### 6.2  Single Responsibility Principle (SRP)

A class should have **one and only one reason to change**.

#### Example (Violation)

```csharp
public class ReportManager
{
    public string GenerateReport() => "Report content";
    public void SaveToFile(string data) { /* file system logic */ }
    public void SendEmail(string data) { /* email logic */ }
}
```

#### Refactored

```csharp
public class ReportGenerator { public string Generate() => "Report"; }
public class FileSaver { public void Save(string data) { /* ... */ } }
public class EmailSender { public void Send(string data) { /* ... */ } }
```

#### Senior Insight

Each class should have a **single axis of change**.
This simplifies unit testing and maintenance by isolating responsibilities.

---

### 6.3  Open/Closed Principle (OCP)

Software entities should be **open for extension but closed for modification**.

#### Example

```csharp
public interface IDiscountStrategy { decimal Apply(decimal total); }

public class NoDiscount : IDiscountStrategy { public decimal Apply(decimal t) => t; }
public class PercentageDiscount : IDiscountStrategy
{
    private readonly decimal _rate;
    public PercentageDiscount(decimal rate) => _rate = rate;
    public decimal Apply(decimal t) => t * (1 - _rate);
}

public class Checkout
{
    private readonly IDiscountStrategy _strategy;
    public Checkout(IDiscountStrategy strategy) => _strategy = strategy;
    public decimal Total(decimal subtotal) => _strategy.Apply(subtotal);
}
```

#### Senior Insight

Introduce **abstractions** for variability.
New behaviors are added by creating new classes, not by editing existing logic.

---

### 6.4  Liskov Substitution Principle (LSP)

Subtypes must be **substitutable** for their base types without altering program correctness.

#### Example

```csharp
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    public int Area() => Width * Height;
}

public class Square : Rectangle
{
    public override int Width { set { base.Width = base.Height = value; } }
    public override int Height { set { base.Width = base.Height = value; } }
}
```

Using `Square` where a `Rectangle` is expected may break logic relying on independent width and height.

#### Senior Insight

Avoid violating behavioral expectations of base classes.
Use **interfaces** or **composition** when inheritance constraints are too strict.

---

### 6.5  Interface Segregation Principle (ISP)

Clients should not be forced to depend on interfaces they do not use.

#### Example (Violation)

```csharp
public interface IPrinter
{
    void Print();
    void Scan();
    void Fax();
}
```

#### Refactored

```csharp
public interface IPrint { void Print(); }
public interface IScan { void Scan(); }
public interface IFax { void Fax(); }

public class SimplePrinter : IPrint
{
    public void Print() { /* Implementation */ }
}
```

#### Senior Insight

Split large interfaces into **cohesive, role-specific ones**.
This avoids unnecessary implementation and improves flexibility.

---

### 6.6  Dependency Inversion Principle (DIP)

High-level modules should not depend on low-level modules; both should depend on **abstractions**.

#### Example

```csharp
public interface IMessageService { void Send(string message); }

public class EmailService : IMessageService
{
    public void Send(string message) => Console.WriteLine($"Email: {message}");
}

public class Notification
{
    private readonly IMessageService _service;
    public Notification(IMessageService service) => _service = service;
    public void Alert(string msg) => _service.Send(msg);
}
```

#### Senior Insight

This principle is the foundation of **Dependency Injection (DI)** frameworks in .NET.
Always depend on abstractions (`IRepository`, `IService`) and register concrete implementations at composition root.

---

### 6.7  Applying SOLID Together

When properly combined:

1. **SRP** keeps classes small.
2. **OCP** makes them extensible.
3. **LSP** ensures reliable substitution.
4. **ISP** isolates interfaces.
5. **DIP** decouples layers.

These create **clean, testable, and scalable** architectures aligned with modern enterprise systems.

#### Example Structure

```
Controllers → Services (Interfaces) → Repositories (Interfaces) → Data Context
```

Each layer depends only on abstractions of the next, promoting separation of concerns.

---

### 6.8  Design Patterns — The Senior’s Toolkit

#### 1. Factory Method

Creates objects without exposing instantiation logic.

```csharp
public abstract class Creator
{
    public abstract IProduct FactoryMethod();
}
public class ConcreteCreator : Creator
{
    public override IProduct FactoryMethod() => new ConcreteProduct();
}
```

#### 2. Strategy Pattern

Encapsulates interchangeable behaviors.

```csharp
public interface ILoggerStrategy { void Log(string msg); }
public class FileLogger : ILoggerStrategy { public void Log(string m) => File.WriteAllText("log.txt", m); }
public class ConsoleLogger : ILoggerStrategy { public void Log(string m) => Console.WriteLine(m); }
```

#### 3. Observer Pattern

Defines dependency of one-to-many objects.

```csharp
public interface IObserver { void Update(string data); }
public interface ISubject
{
    void Attach(IObserver o);
    void Detach(IObserver o);
    void Notify(string data);
}
```

#### Senior Insight

Patterns must be used **intentionally**, not ceremonially.
A senior engineer recognizes *why* a pattern exists and *when not to use it*.

---

### 6.9  Clean Architecture Principles

* **Entities (Domain)** — core business rules.
* **Use Cases (Application)** — orchestrate workflows.
* **Adapters (Infrastructure)** — implementation details like DB or APIs.
* **Interface Boundaries** — enforce one-way dependencies toward the domain.

#### Senior Insight

This separation ensures maintainability and testability.
Changes in frameworks or infrastructure never affect core business logic.

---

### 6.10  Testing and Maintainability

* Write **unit tests** for individual responsibilities.
* Mock dependencies using interfaces.
* Validate SOLID adherence by analyzing class dependencies.
* Use **code metrics**: cyclomatic complexity, coupling, and maintainability index.

#### Example

```csharp
[Fact]
public void CalculatePay_ReturnsMonthlySalary()
{
    var emp = new SalariedEmployee { AnnualSalary = 120000 };
    Assert.Equal(10000, emp.CalculatePay());
}
```

#### Senior Insight

A well-structured OOP design leads to a natural test architecture.
Testing is the first indicator that your SOLID implementation works.
