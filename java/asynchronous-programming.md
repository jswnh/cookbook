# 1. Synchronous vs Asynchronous Execution

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

## Synchronous Execution

Operations execute sequentially. The invoking thread remains blocked, waiting for the operation (such as disk I/O, database query, or remote HTTP call) to complete.

```java
User user = repository.findUserById("USR-101");
System.out.println(user.name());
```

Execution flow:

```text
findUserById()
      ↓
Thread is blocked waiting for I/O
      ↓
Operation completes
      ↓
Execution continues
```

---

## Asynchronous Execution

The initiating thread triggers the task and immediately returns to continue other work. The task executes concurrently in the background and notifies or resumes when finished.

```java
CompletableFuture.supplyAsync(() -> repository.findUserById("USR-101"))
                 .thenAccept(user -> System.out.println(user.name()));
```

Execution flow:

```text
supplyAsync()
      ↓
Returns CompletableFuture immediately
      ↓
Calling thread remains free to handle other requests
      ↓
Background operation finishes
      ↓
Completion callback executed
```

---

# 2. Concurrency Evolution in Java

| Generation | Era / Version | Core Model | Strengths / Limitations |
| :--- | :--- | :--- | :--- |
| **1. Classic Threads** | Java 1.0 | `java.lang.Thread`, `Runnable` | Direct 1:1 mapping to heavy OS threads. Expensive to create (~1MB stack per thread). |
| **2. Thread Pools** | Java 5 | `ExecutorService`, `Future<V>` | Reuses platform threads to prevent thread-exhaustion crashes. |
| **3. Non-blocking Pipelines**| Java 8 | `CompletableFuture<T>` | Rich monadic chaining and reactive compositions without blocking threads. |
| **4. Virtual Threads** | Java 21+ | Project Loom (`Thread.ofVirtual()`) | Millions of cheap user-mode threads scheduled by the JVM over carrier threads. |
| **5. Structured Concurrency**| Java 21 - 26 | `StructuredTaskScope`, `ScopedValue` | Bounded lifecycles, automatic cancellation, and thread-safe data sharing across virtual threads. |

---

# 3. CompletableFuture<T>

`CompletableFuture<T>` provides asynchronous programming using functional composition.

## Starting Asynchronous Tasks

```java
import java.util.concurrent.CompletableFuture;

// Asynchronous computation returning a value
CompletableFuture<String> futureResult = CompletableFuture.supplyAsync(() -> {
    // Simulates I/O or background calculation
    return "Query Result";
});

// Asynchronous action returning void
CompletableFuture<Void> futureAction = CompletableFuture.runAsync(() -> {
    System.out.println("Background telemetry logged");
});
```

---

## Transforming Results (`thenApply`)

Maps the completed result to a new value synchronously:

```java
CompletableFuture<Integer> lengthFuture = CompletableFuture.supplyAsync(() -> "Hello, Java 26")
        .thenApply(String::length);

lengthFuture.thenAccept(len -> System.out.println("Length: " + len));
```

---

## Chaining Asynchronous Calls (`thenCompose`)

Flattens nested futures (`CompletableFuture<CompletableFuture<U>>` into `CompletableFuture<U>`), equivalent to `flatMap`.

```java
public CompletableFuture<User> fetchUserAsync(String id) {
    return CompletableFuture.supplyAsync(() -> new User(id, "Alice"));
}

public CompletableFuture<Double> fetchBalanceAsync(User user) {
    return CompletableFuture.supplyAsync(() -> 1540.50);
}

// Chaining dependent asynchronous calls
CompletableFuture<Double> userBalance = fetchUserAsync("USR-101")
        .thenCompose(this::fetchBalanceAsync);
```

---

## Combining Independent Futures (`thenCombine`)

Executes two futures in parallel and executes a bifunction when both have completed:

```java
CompletableFuture<String> userFuture = CompletableFuture.supplyAsync(() -> "Josuan");
CompletableFuture<Integer> scoreFuture = CompletableFuture.supplyAsync(() -> 98);

CompletableFuture<String> combined = userFuture.thenCombine(scoreFuture, 
    (name, score) -> "%s scored %d points".formatted(name, score)
);

System.out.println(combined.join()); // Josuan scored 98 points
```

---

## Exception Handling

```java
CompletableFuture<String> secureFetch = CompletableFuture.supplyAsync(() -> {
    if (Math.random() > 0.5) {
        throw new IllegalStateException("Network timeout");
    }
    return "Payload Loaded";
}).exceptionally(ex -> {
    System.err.println("Error encountered: " + ex.getMessage());
    return "Default Fallback Data";
});
```

---

# 4. Virtual Threads (Project Loom in JDK 26)

Virtual Threads are lightweight threads managed entirely by the Java runtime rather than the operating system. They enable the simplicity of the synchronous "thread-per-request" programming model with the scalability of asynchronous reactive architectures.

## Platform Threads vs Virtual Threads

```text
Platform Thread (1:1 with OS kernel thread):
[ Java Thread ] <---> [ OS Kernel Thread ] (~1MB stack, limited to ~1,000-5,000 threads)

Virtual Thread (M:N JVM scheduling):
[ Virtual Thread 1 ] \
[ Virtual Thread 2 ]  ---> [ Carrier Platform Thread ] ---> [ OS Kernel Thread ]
[ Virtual Thread N ] /      (Few hundred bytes, millions can run concurrently)
```

---

## Starting a Virtual Thread

```java
// Starting an unstarted virtual thread
Thread vThread = Thread.ofVirtual()
                       .name("worker-vthread")
                       .start(() -> {
                           System.out.println("Running on: " + Thread.currentThread());
                       });

vThread.join();

// Quick static launcher
Thread.startVirtualThread(() -> {
    System.out.println("Lightweight background task");
});
```

---

## Virtual Thread Per-Task Executor

Using virtual threads with standard `ExecutorService` and `try-with-resources`:

```java
import java.util.concurrent.Executors;

try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 10_000; i++) {
        final int taskId = i;
        executor.submit(() -> {
            Thread.sleep(100); // Does NOT block OS thread; carrier thread is unmounted!
            return taskId;
        });
    }
} // Automatically awaits completion of all virtual thread tasks upon closing
```

> [!TIP]
> Do **not** pool virtual threads! Virtual threads are disposable and lightweight. Create them on-demand per task.

---

# 5. Structured Concurrency (Java 26)

Structured Concurrency treats multiple subtasks executing in separate threads as a single atomic unit of work, preventing thread leaks and simplifying error handling and cancellation.

## `ShutdownOnFailure` (All Must Succeed)

Runs tasks concurrently; if any subtask fails, all remaining subtasks are automatically cancelled.

```java
import java.util.concurrent.StructuredTaskScope;
import java.util.concurrent.StructuredTaskScope.Subtask;

public record UserDashboard(String profile, String orders) {}

public UserDashboard fetchDashboard(String userId) throws Exception {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        Subtask<String> profileSubtask = scope.fork(() -> fetchProfile(userId));
        Subtask<String> ordersSubtask = scope.fork(() -> fetchOrders(userId));

        scope.join();           // Wait for both subtasks
        scope.throwIfFailed();  // Propagate first failure if any failed

        // Both succeeded
        return new UserDashboard(profileSubtask.get(), ordersSubtask.get());
    }
}

private String fetchProfile(String id) { return "ProfileData"; }
private String fetchOrders(String id) { return "OrdersList"; }
```

---

## `ShutdownOnSuccess` (Race to Fastest Result)

Returns the result of the first successful subtask and cancels all other pending subtasks.

```java
import java.util.concurrent.StructuredTaskScope;

public String fetchFastestMirror() throws Exception {
    try (var scope = new StructuredTaskScope.ShutdownOnSuccess<String>()) {
        scope.fork(() -> queryServer("https://us-east.api.com"));
        scope.fork(() -> queryServer("https://eu-west.api.com"));
        scope.fork(() -> queryServer("https://ap-southeast.api.com"));

        scope.join(); // Wait until the first subtask completes successfully
        return scope.result(); // Retrieves winner
    }
}

private String queryServer(String url) { return "Response from " + url; }
```

---

# 6. Scoped Values (Java 26)

`ScopedValue<T>` provides an immutable, lightweight mechanism for passing contextual data (e.g., security context, tenant ID, transaction trace ID) down call stacks and across virtual threads, eliminating the overhead and memory leak risks of `ThreadLocal`.

```java
import java.lang.ScopedValue;

public class SecurityContextHolder {
    public static final ScopedValue<String> CURRENT_USER = ScopedValue.newInstance();

    public static void main(String[] args) {
        // Bind "admin-josuan" to CURRENT_USER for the duration of the lambda
        ScopedValue.where(CURRENT_USER, "admin-josuan").run(() -> {
            processRequest();
        });
    }

    public static void processRequest() {
        System.out.println("Authenticated User: " + CURRENT_USER.get());
    }
}
```

---

# 7. Real-World Example

High-throughput concurrent e-commerce aggregation service leveraging Virtual Threads and Structured Concurrency.

```java
import java.util.concurrent.StructuredTaskScope;
import java.util.concurrent.StructuredTaskScope.Subtask;

public class OrderAggregationService {
    public record OrderSummary(String orderId, String customer, double totalAmount, String shippingStatus) {}

    public static OrderSummary aggregate(String orderId) throws Exception {
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
            Subtask<String> customerTask = scope.fork(() -> {
                Thread.sleep(50); // Simulate API latency
                return "Alice Smith";
            });

            Subtask<Double> totalTask = scope.fork(() -> {
                Thread.sleep(40);
                return 349.95;
            });

            Subtask<String> shippingTask = scope.fork(() -> {
                Thread.sleep(60);
                return "DISPATCHED";
            });

            scope.join();
            scope.throwIfFailed();

            return new OrderSummary(orderId, customerTask.get(), totalTask.get(), shippingTask.get());
        }
    }

    public static void main(String[] args) throws Exception {
        OrderSummary summary = aggregate("ORD-9801");
        System.out.printf("Order: %s | Customer: %s | Total: $%.2f | Status: %s%n",
            summary.orderId(),
            summary.customer(),
            summary.totalAmount(),
            summary.shippingStatus()
        );
    }
}
```

Output:

```text
Order: ORD-9801 | Customer: Alice Smith | Total: $349.95 | Status: DISPATCHED
```

---

# Summary

| Technology | Purpose | Recommended Use |
| :--- | :--- | :--- |
| **`CompletableFuture`** | Functional, non-blocking asynchronous event pipelines | Reactive compositions, event-driven callback graphs |
| **Virtual Threads** | Lightweight user-mode threads (`Executors.newVirtualThreadPerTaskExecutor()`) | High-concurrency I/O-heavy applications (HTTP, DB queries) |
| **Structured Concurrency** | Coordinated lifecycle for concurrent subtasks (`StructuredTaskScope`) | Running concurrent fan-out tasks with automatic cancellation |
| **`ScopedValue`** | Immutable context propagation | Passing request security credentials, trace IDs across virtual tasks |
