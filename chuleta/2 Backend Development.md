## 2  Backend Development with C# and .NET

---

### 2.1  Understanding IEnumerable, IQueryable, and List<T>

These interfaces and classes represent different ways of working with data collections in C#.

| Type               | Description                                                               | Evaluation                                | Typical Use                                       |
| :----------------- | :------------------------------------------------------------------------ | :---------------------------------------- | :------------------------------------------------ |
| **IEnumerable<T>** | Basic forward-only iterator. Executes queries **in memory**.              | Deferred (evaluated when iterated).       | Local operations on collections.                  |
| **IQueryable<T>**  | Extends IEnumerable to support **query translation** (e.g., LINQ-to-SQL). | Deferred until sent to the data provider. | Database or remote queries.                       |
| **List<T>**        | Concrete collection stored in memory.                                     | Immediate.                                | When random access and modification are required. |

#### Example

```csharp
IEnumerable<int> list = new List<int> {1, 2, 3, 4, 5};
var even = list.Where(x => x % 2 == 0);   // executed when enumerated

IQueryable<User> users = db.Users.Where(u => u.Age > 30);
// translated to SQL and executed by the provider
```

#### Senior Insight

Use `IQueryable` for database queries to leverage the provider’s optimization.
Materialize results with `ToList()` only when necessary to avoid unnecessary memory allocation.

---

### 2.2  LINQ (Language Integrated Query)

LINQ integrates querying directly into C# syntax.

#### Example

```csharp
var result = from u in users
             where u.IsActive
             orderby u.LastLogin descending
             select new { u.Id, u.Name };
```

LINQ expressions can target:

* **LINQ-to-Objects** (in-memory collections)
* **LINQ-to-Entities** (Entity Framework)
* **LINQ-to-XML** or **LINQ-to-SQL**

#### Senior Insight

Prefer expression syntax for readability; avoid complex joins in LINQ when SQL-level optimization is required.
Profile queries with logging or `ToQueryString()` to ensure proper translation.

---

### 2.3  DataContext and Entity Framework Core

`DbContext` represents the gateway between code and the database.

#### Key Responsibilities

* Tracks entity states (Added, Modified, Deleted).
* Manages connections and transactions.
* Persists data via `SaveChanges()`.

#### Example

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }
}
```

#### Senior Insight

Use a single `DbContext` per logical unit of work.
Avoid long-lived contexts—they cache data and can degrade performance or cause stale entities.

---

### 2.4  Delegates, Func, Action, Predicate

Delegates are **type-safe references to methods**.

| Type               | Signature         | Purpose                  |
| :----------------- | :---------------- | :----------------------- |
| `delegate`         | Custom definition | Strongly typed callback. |
| `Func<T, TResult>` | Returns a value   | Transformations.         |
| `Action<T>`        | Returns void      | Execute operations.      |
| `Predicate<T>`     | Returns bool      | Conditional evaluation.  |

#### Example

```csharp
Func<int, int> square = x => x * x;
Action<string> print = s => Console.WriteLine(s);
Predicate<int> isEven = n => n % 2 == 0;

if (isEven(4)) print($"Square: {square(4)}");
```

#### Senior Insight

Delegates enable event-driven architectures and functional patterns.
Combined with LINQ and `async/await`, they form the foundation for reactive pipelines in C#.

---

### 2.5  Async / Await and ConfigureAwait

#### Concept

`async` and `await` simplify asynchronous programming by allowing non-blocking code to appear sequential.

#### Example

```csharp
public async Task<string> FetchAsync()
{
    using var client = new HttpClient();
    string data = await client.GetStringAsync("https://api.example.com");
    return data;
}
```

* `await` pauses execution until the awaited task completes.
* Control returns to the caller without blocking the thread.

#### ConfigureAwait

```csharp
await SomeTask().ConfigureAwait(false);
```

By passing `false`, continuation is scheduled **without capturing the original synchronization context**, improving performance in library code.

#### Senior Insight

Always apply `ConfigureAwait(false)` in reusable libraries to avoid deadlocks in UI contexts (e.g., WinForms, WPF).
Use `Task.WhenAll()` for parallel awaits instead of sequential chaining.

---

### 2.6  Thread Safety, Multithreading, and Synchronization

Concurrency introduces race conditions when multiple threads access shared data.

#### Common Mechanisms

| Concept                       | Description                                   |
| :---------------------------- | :-------------------------------------------- |
| **Lock (`lock`)**             | Ensures exclusive access to a code block.     |
| **Monitor / Mutex**           | OS-level lock, cross-process capable.         |
| **Semaphore / SemaphoreSlim** | Limits concurrent access to a resource.       |
| **Thread-safe collections**   | `ConcurrentDictionary`, `BlockingCollection`. |

#### Example

```csharp
private static readonly object _lock = new();

public void Increment()
{
    lock (_lock)
    {
        counter++;
    }
}
```

#### Senior Insight

Avoid over-synchronization; prefer immutable data or concurrent collections.
Use asynchronous concurrency (`Task`, `Channel`, `Parallel.ForEachAsync`) instead of raw threads whenever possible.

---

### 2.7  Yield and Iterators

`yield` allows a method to return elements **lazily**, maintaining its state between iterations.

#### Example

```csharp
public static IEnumerable<int> Range(int start, int count)
{
    for (int i = start; i < start + count; i++)
        yield return i;
}
```

Each `yield return` pauses the method until the next element is requested.

#### Senior Insight

Use `yield` to build pipelines that consume minimal memory, especially for streaming data or large collections.

---

### 2.8  Attributes and Reflection

Attributes add **metadata** to code elements (classes, methods, parameters) and are processed at runtime via Reflection.

#### Example

```csharp
[AttributeUsage(AttributeTargets.Class)]
public class AuditableAttribute : Attribute
{
    public string Entity { get; }
    public AuditableAttribute(string entity) => Entity = entity;
}

[Auditable("Customer")]
public class Customer { }
```

#### Reflection Usage

```csharp
var attr = typeof(Customer)
           .GetCustomAttributes(typeof(AuditableAttribute), false)
           .FirstOrDefault() as AuditableAttribute;
Console.WriteLine(attr.Entity); // Customer
```

#### Senior Insight

Custom attributes power cross-cutting features such as logging, validation, and serialization.
Be cautious with reflection—it incurs runtime cost; cache results for repeated access.

---

### 2.9  Thread Pools and Task Schedulers

The .NET runtime manages a **Thread Pool** to execute asynchronous tasks efficiently.

* Avoid manually creating threads; use `Task.Run()` or background services.
* Use `CancellationToken` to enable cooperative task cancellation.
* Use `TaskScheduler.Default` for CPU-bound operations and `ConfigureAwait(false)` for library code.

#### Senior Insight

Always benchmark asynchronous code under realistic load; thread-pool starvation or improper locking can cause severe latency in production APIs.

---

### 2.10  Error Handling and Logging Patterns

#### Structured Exception Handling

```csharp
try
{
    var result = Process();
}
catch (CustomException ex)
{
    _logger.LogError(ex, "Custom failure");
}
finally
{
    CleanResources();
}
```

#### Best Practices

* Never catch `Exception` unless rethrowing or logging globally.
* Use **typed exceptions** and meaningful messages.
* Integrate **logging frameworks** (Serilog, NLog, Microsoft Extensions Logging) with structured output (JSON).

#### Senior Insight

Centralize exception handling with middleware in ASP.NET Core.
Log contextually: include correlation IDs and user/session information for distributed tracing.
