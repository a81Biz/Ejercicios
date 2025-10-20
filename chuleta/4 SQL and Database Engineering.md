## 4  SQL and Database Engineering

---

### 4.1  Primary Key vs. Unique Key

Both constraints enforce data integrity, but their purposes differ.

| Feature             | Primary Key                     | Unique Key                                        |
| :------------------ | :------------------------------ | :------------------------------------------------ |
| **Purpose**         | Identifies each record uniquely | Ensures uniqueness of data in one or more columns |
| **Null Values**     | Not allowed                     | Allowed (one `NULL` per column)                   |
| **Index Type**      | Clustered by default            | Non-clustered by default                          |
| **Count per Table** | Only one                        | Multiple allowed                                  |

#### Example

```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Email NVARCHAR(100) UNIQUE,
    Name NVARCHAR(50)
);
```

#### Senior Insight

Always choose **natural vs. surrogate keys** consciously.
Use surrogate (`INT IDENTITY` or `UUID`) when the natural key may change, to preserve relational integrity.

---

### 4.2  WHERE Clause vs. HAVING Clause

Both filter records, but operate at different query stages.

| Clause     | Filters            | Usage           |
| :--------- | :----------------- | :-------------- |
| **WHERE**  | Individual rows    | Before grouping |
| **HAVING** | Aggregated results | After grouping  |

#### Example

```sql
SELECT Department, COUNT(*) AS Employees
FROM Staff
WHERE IsActive = 1
GROUP BY Department
HAVING COUNT(*) > 5;
```

#### Senior Insight

Avoid filtering with `HAVING` unless it depends on an aggregate; otherwise, performance degrades because grouping occurs unnecessarily.

---

### 4.3  GROUP BY and Aggregate Functions

`GROUP BY` combines rows with the same values into summary rows.

#### Example

```sql
SELECT Department, AVG(Salary) AS AvgSalary
FROM Employees
GROUP BY Department;
```

#### Common Aggregate Functions

| Function          | Purpose        |
| :---------------- | :------------- |
| `COUNT()`         | Number of rows |
| `SUM()`           | Total value    |
| `AVG()`           | Average value  |
| `MIN()` / `MAX()` | Extremes       |

#### Senior Insight

Always include non-aggregated columns in the `GROUP BY` clause to maintain deterministic results.
Use `ROLLUP` or `CUBE` for multidimensional summaries in analytics.

---

### 4.4  Transactions and Isolation Levels

#### Concept

A **transaction** is a sequence of operations performed as a single logical unit of work that must exhibit **ACID** properties:

| Property        | Definition                                            |
| :-------------- | :---------------------------------------------------- |
| **Atomicity**   | All operations succeed or none do.                    |
| **Consistency** | Database transitions from one valid state to another. |
| **Isolation**   | Concurrent transactions do not interfere.             |
| **Durability**  | Once committed, data persists even after failures.    |

#### Example

```sql
BEGIN TRANSACTION;
UPDATE Accounts SET Balance = Balance - 500 WHERE Id = 1;
UPDATE Accounts SET Balance = Balance + 500 WHERE Id = 2;
COMMIT;
```

#### Isolation Levels

| Level                | Phenomena Prevented                    | Description                            |
| :------------------- | :------------------------------------- | :------------------------------------- |
| **READ UNCOMMITTED** | None                                   | Allows dirty reads.                    |
| **READ COMMITTED**   | Dirty reads                            | Default in SQL Server.                 |
| **REPEATABLE READ**  | Dirty + non-repeatable reads           | Prevents re-reading changed rows.      |
| **SERIALIZABLE**     | Dirty + non-repeatable + phantom reads | Highest isolation, lowest concurrency. |

#### Senior Insight

Use `READ COMMITTED SNAPSHOT` to balance consistency and concurrency.
Long transactions cause blocking—commit early and keep locks granular.

---

### 4.5  Clustered vs. Non-Clustered Indexes

Indexes improve lookup performance by maintaining an ordered structure.

| Type              | Description                                      | Count per Table | Structure                         |
| :---------------- | :----------------------------------------------- | :-------------- | :-------------------------------- |
| **Clustered**     | Defines physical storage order of data           | 1               | B-Tree, data stored at leaf nodes |
| **Non-Clustered** | Separate structure storing key + pointer to data | Many            | B-Tree referencing clustered key  |

#### Example

```sql
CREATE CLUSTERED INDEX IX_Employees_Id ON Employees(EmployeeID);
CREATE NONCLUSTERED INDEX IX_Employees_Email ON Employees(Email);
```

#### Senior Insight

* Keep clustered index narrow and stable (e.g., `INT IDENTITY`).
* Avoid over-indexing: each index slows down write operations.
* Use **covering indexes** to satisfy queries entirely from the index (no key lookups).

---

### 4.6  Query Execution and Optimization

#### Execution Phases

1. **Parsing** → SQL syntax validation.
2. **Optimization** → Query plan generation.
3. **Execution** → Retrieval using indexes or scans.

Use:

```sql
SET SHOWPLAN_ALL ON;
```

or in SQL Server Management Studio:

```sql
EXPLAIN
```

to view the plan.

#### Common Join Types

| Type           | Description                              |
| :------------- | :--------------------------------------- |
| **INNER JOIN** | Matches rows in both tables.             |
| **LEFT JOIN**  | All rows from left + matched from right. |
| **RIGHT JOIN** | All rows from right + matched from left. |
| **FULL JOIN**  | Combines left and right, includes nulls. |

#### Senior Insight

* Compare **estimated** vs **actual** execution plans to identify key lookups, scans, or missing indexes.
* Use **parameterized queries** to reduce recompilations.
* Cache execution plans with `sp_executesql`.

---

### 4.7  Normalization and Denormalization

#### Normalization

Process of reducing redundancy by dividing data into related tables.

| Normal Form | Rule                                        |
| :---------- | :------------------------------------------ |
| **1NF**     | No repeating groups or arrays.              |
| **2NF**     | All non-key columns depend on the full key. |
| **3NF**     | No transitive dependencies.                 |

#### Denormalization

Combining tables to optimize read performance—useful in OLAP systems.

#### Senior Insight

Normalize for OLTP, denormalize for analytics.
For high-load systems, maintain dual models via ETL pipelines.

---

### 4.8  Stored Procedures, Functions, and Triggers

#### Stored Procedure

```sql
CREATE PROCEDURE GetEmployee @Id INT
AS
BEGIN
  SELECT * FROM Employees WHERE EmployeeID = @Id;
END;
```

#### Function

```sql
CREATE FUNCTION GetTotalSalary()
RETURNS MONEY
AS
BEGIN
  RETURN (SELECT SUM(Salary) FROM Employees);
END;
```

#### Trigger

```sql
CREATE TRIGGER AuditEmployeeInsert
ON Employees
AFTER INSERT
AS
INSERT INTO AuditLog(Entity, ActionDate)
SELECT 'Employee', GETDATE();
```

#### Senior Insight

Use stored procedures for encapsulating business logic and minimizing round trips.
Avoid triggers for complex logic—they obscure side effects and hinder performance.

---

### 4.9  High-Performance Design Patterns

* Use **batch inserts** and **table-valued parameters** for bulk operations.
* Use **pagination with OFFSET/FETCH** instead of `TOP` when streaming large result sets.
* Leverage **partitioned tables** for massive data volumes.
* Maintain **statistics** updated for accurate query plans.

#### Example

```sql
SELECT * FROM Orders
ORDER BY OrderDate
OFFSET 100 ROWS FETCH NEXT 50 ROWS ONLY;
```

#### Senior Insight

Performance is context-dependent. Always test on realistic data volumes.
Monitor I/O, CPU, and waits using `sys.dm_exec_query_stats`.

---

### 4.10  Security in SQL

| Threat                   | Mitigation                                                 |
| :----------------------- | :--------------------------------------------------------- |
| **SQL Injection**        | Use parameterized queries.                                 |
| **Privilege Escalation** | Apply least privilege principle.                           |
| **Data Exposure**        | Encrypt columns and use Transparent Data Encryption (TDE). |
| **Auditing**             | Enable SQL Server Audit or Azure Defender for SQL.         |

#### Example (Parameterized Query)

```csharp
cmd.CommandText = "SELECT * FROM Users WHERE Username = @u";
cmd.Parameters.AddWithValue("@u", username);
```

#### Senior Insight

All database access should go through validated APIs.
Never concatenate user input into SQL strings.
