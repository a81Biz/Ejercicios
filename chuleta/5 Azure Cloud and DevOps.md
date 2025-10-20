## 5  Azure Cloud and DevOps

---

### 5.1  Cosmos DB and Partitioning

**Azure Cosmos DB** is Microsoft’s fully managed, globally distributed NoSQL database designed for low-latency access and massive scale.

#### Key Concepts

| Term                                | Definition                                                   |
| :---------------------------------- | :----------------------------------------------------------- |
| **Container**                       | A collection of items (documents, rows, etc.).               |
| **Partition Key**                   | Attribute used to distribute data across logical partitions. |
| **Physical Partition**              | Actual storage unit managed by Cosmos DB.                    |
| **RU/s** (Request Units per second) | Measure of throughput capacity.                              |

#### Example

```json
{
  "id": "1",
  "userId": "42",
  "region": "us-west",
  "items": ["A", "B"]
}
```

Partition Key → `userId`

#### Senior Insight

Choose partition keys with **high cardinality** and **even access distribution**.
Avoid “hot partitions” where most requests hit the same key.
Leverage **Change Feed** to react to data mutations in real time (event-driven pipelines).

---

### 5.2  Azure Service Bus and Messaging Patterns

A **Service Bus** decouples microservices via asynchronous communication.

#### Concepts

* **Queue** – Point-to-point communication (1 sender, 1 receiver).
* **Topic / Subscription** – Publish-subscribe pattern (1 publisher, many subscribers).
* **Sessions** – Maintain ordering and stateful processing.
* **Dead-Letter Queue** – Stores messages that cannot be delivered or processed.

#### Example

```csharp
await using var client = new ServiceBusClient(connStr);
ServiceBusSender sender = client.CreateSender("orders");
await sender.SendMessageAsync(new ServiceBusMessage("Order Created"));
```

#### Senior Insight

Implement **idempotent consumers** so retries don’t duplicate work.
Use **peek-lock mode** for safe processing (acknowledge after completion).
For high throughput, prefer **batch sending** and **prefetching**.

---

### 5.3  Continuous Integration / Continuous Delivery (CI/CD)

Automation is central to modern DevOps.
**YAML-based pipelines** describe build and deployment steps as code.

#### Example (GitHub Actions)

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: 8.0.x
      - run: dotnet restore
      - run: dotnet build --configuration Release
      - run: dotnet test
      - name: Publish
        run: dotnet publish -c Release -o out
      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v3
        with:
          app-name: my-dotnet-api
          package: out
```

#### Senior Insight

* Enforce **build quality gates** (tests, lint, security scan).
* Use **multi-stage pipelines** (build → test → deploy).
* Store secrets in Azure Key Vault or GitHub Secrets.
* Implement **blue/green** or **canary** deployments for zero downtime.

---

### 5.4  Scalability Strategies

#### Vertical Scaling

Increase CPU and RAM on existing instances.
Quick but limited by hardware.

#### Horizontal Scaling

Add more instances behind a load balancer.
Ideal for stateless services.

#### Patterns

| Scenario   | Pattern                                             | Description                                            |
| :--------- | :-------------------------------------------------- | :----------------------------------------------------- |
| High Read  | **Replication**                                     | Read replicas serve queries to reduce load on primary. |
| High Write | **Partitioning / Sharding**                         | Split data by key or region.                           |
| Both High  | **CQRS** (Command-Query Responsibility Segregation) | Separate read and write models.                        |

#### Senior Insight

* Cache frequently read data using **Azure Redis Cache**.
* Offload static assets to **CDN**.
* For write-heavy systems, queue workloads to **Service Bus** or **Event Hub**.
* Monitor and auto-scale based on **CPU**, **requests per second**, or **queue depth**.

---

### 5.5  High Read and High Write Scenarios

#### High Read

* Implement **in-memory caching** (ASP.NET Core MemoryCache or Redis).
* Enable **read replicas** and **geo-replication**.
* Use **CDN edge nodes** to serve content near users.

#### High Write

* Partition the workload (logical or physical sharding).
* Use **asynchronous queue-based writes** to avoid blocking.
* Employ **bulk insert/update** operations instead of row-by-row.

#### Senior Insight

Balance throughput and consistency with **eventual consistency** models.
Design idempotent operations so retries do not corrupt data.

---

### 5.6  SignalR and Real-Time Notifications

**SignalR** enables server-to-client communication in real time via WebSockets or fallback transport mechanisms.

#### Example (Server)

```csharp
public class NotificationHub : Hub
{
  public async Task SendMessage(string user, string message)
  {
    await Clients.All.SendAsync("ReceiveMessage", user, message);
  }
}
```

#### Example (Client JavaScript)

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl("/notificationHub")
  .build();

connection.on("ReceiveMessage", (user, msg) => {
  console.log(`${user}: ${msg}`);
});

await connection.start();
await connection.invoke("SendMessage", "Admin", "Hello world");
```

#### Senior Insight

SignalR is ideal for notifications, chat, IoT telemetry, and dashboard updates.
In Azure, use **Azure SignalR Service** to offload connections and scale globally.

---

### 5.7  Monitoring and Observability in Azure

| Tool                     | Purpose                                                   |
| :----------------------- | :-------------------------------------------------------- |
| **Application Insights** | Collects telemetry, traces, and exceptions.               |
| **Azure Monitor**        | Central metrics and alerts dashboard.                     |
| **Log Analytics**        | Query and correlate logs with Kusto Query Language (KQL). |
| **Azure Dashboards**     | Custom visualization of KPIs.                             |

#### Example Metric Query

```kusto
requests
| where success == false
| summarize count() by url
```

#### Senior Insight

* Tag telemetry with **correlation IDs** to trace requests across microservices.
* Use **distributed tracing** (OpenTelemetry / Application Insights SDK).
* Establish SLIs (Service Level Indicators) and SLOs (Service Level Objectives) for visibility.

---

### 5.8  Cost Optimization and Governance

* Use **Reserved Instances** and **Savings Plans** for predictable workloads.
* Implement **Auto-Shutdown** for non-production environments.
* Set **budgets** and alerts in Azure Cost Management.
* Use **Resource Tags** for tracking ownership and billing.
* Apply **Azure Policy** to enforce standards (region, SKU, naming).

#### Senior Insight

Architect for cost visibility early.
Untracked resources can quickly accumulate significant expense.
