# 1. Synchronous vs Asynchronous Execution

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

## Synchronous Execution

Operations execute sequentially. The calling thread remains blocked while waiting for I/O operations (network, disk, or database) to complete.

```vb
Dim data = repository.GetUsers()
Console.WriteLine(data.Count)
```

Execution flow:

```text
GetUsers()
   ↓
Calling thread is blocked waiting
   ↓
Data arrives
   ↓
Execution resumes
```

---

## Asynchronous Execution

Operations yield control back to the caller while the operation is pending, freeing the thread to process other requests or maintain responsive user interfaces.

```vb
Dim data = Await repository.GetUsersAsync()
Console.WriteLine(data.Count)
```

Execution flow:

```text
GetUsersAsync()
   ↓
Returns Task immediately
   ↓
Calling thread is released to thread pool / UI loop
   ↓
Background I/O completes
   ↓
Execution resumes after Await
```

---

# 2. Task and Task(Of T)

The foundation of asynchronous programming in modern .NET is the `Task` type from `System.Threading.Tasks`.

- **`Task`**: Represents an asynchronous operation that returns no value (equivalent to `Sub`).
- **`Task(Of T)`**: Represents an asynchronous operation that returns a value of type `T` (equivalent to `Function`).

```vb
Imports System
Imports System.Threading.Tasks

Public Module TaskBasics
    ' Asynchronous Sub-like operation
    Public Async Function SendEmailAsync() As Task
        Await Task.Delay(1000) ' Non-blocking delay
        Console.WriteLine("Email sent successfully")
    End Function

    ' Asynchronous Function-like operation returning data
    Public Async Function CalculateTotalAsync() As Task(Of Decimal)
        Await Task.Delay(500)
        Return 299.95D
    End Function
End Module
```

> [!WARNING]
> Always use `Async Function ... As Task` instead of `Async Sub`, except for top-level event handlers. `Async Sub` methods cannot be awaited and unhandled exceptions cannot be caught by callers.

---

# 3. Running Concurrent Tasks (`Task.WhenAll` and `Task.WhenAny`)

## `Task.WhenAll` (Wait for All)

Runs multiple tasks concurrently and waits for all of them to complete.

```vb
Imports System.Threading.Tasks

Public Async Function LoadDashboardDataAsync() As Task
    Dim userTask = FetchUserProfileAsync()
    Dim metricsTask = FetchMetricsAsync()

    ' Both tasks execute in parallel
    Await Task.WhenAll(userTask, metricsTask)

    Dim user = userTask.Result
    Dim metrics = metricsTask.Result

    Console.WriteLine($"Dashboard loaded for {user.Name}")
End Function
```

---

## `Task.WhenAny` (Wait for First)

Races multiple tasks and completes as soon as any one task finishes.

```vb
Public Async Function FetchFastestMirrorAsync() As Task(Of String)
    Dim task1 = QueryServerAsync("https://us.api.com")
    Dim task2 = QueryServerAsync("https://eu.api.com")

    Dim completedTask = Await Task.WhenAny(task1, task2)
    Return Await completedTask
End Function
```

---

# 4. Cancellation Tokens

Allows long-running asynchronous tasks to be cancelled gracefully.

```vb
Imports System
Imports System.Threading
Imports System.Threading.Tasks

Public Async Function ProcessLargeBatchAsync(token As CancellationToken) As Task
    For i As Integer = 1 To 100
        token.ThrowIfCancellationRequested()

        Await Task.Delay(50, token)
        Console.WriteLine($"Processed item {i}")
    Next
End Function

' Usage with CancellationTokenSource
Dim cts As New CancellationTokenSource()
cts.CancelAfter(TimeSpan.FromSeconds(2)) ' Automatically cancel after 2 seconds
```

---

# 5. Exception Handling & Filters

Use standard `Try...Catch` blocks with `Await`. VB.NET also supports concise **Exception Filters** using the `When` keyword.

```vb
Imports System
Imports System.Net.Http
Imports System.Threading.Tasks

Public Async Function DownloadDataAsync(url As String) As Task(Of String)
    Using client As New HttpClient()
        Try
            Return Await client.GetStringAsync(url)
        Catch ex As HttpRequestException When ex.StatusCode = Net.HttpStatusCode.NotFound
            Console.WriteLine("Requested resource not found (404)")
            Return String.Empty
        Catch ex As HttpRequestException
            Console.WriteLine($"Network error: {ex.Message}")
            Throw
        Catch ex As Exception
            Console.WriteLine($"Unexpected error: {ex.Message}")
            Throw
        End Try
    End Using
End Function
```

---

# 6. Real-World Example

High-throughput asynchronous order aggregator fetching profile, order items, and shipping status concurrently.

```vb
Imports System
Imports System.Threading.Tasks

Public Module OrderAggregationService
    Public Structure OrderSummary
        Public Property OrderId As String
        Public Property CustomerName As String
        Public Property TotalAmount As Decimal
        Public Property Status As String
    End Structure

    Private Async Function GetCustomerNameAsync(id As String) As Task(Of String)
        Await Task.Delay(40) ' Simulate network call
        Return "Alice Smith"
    End Function

    Private Async Function GetOrderTotalAsync(id As String) As Task(Of Decimal)
        Await Task.Delay(50)
        Return 349.95D
    End Function

    Private Async Function GetShippingStatusAsync(id As String) As Task(Of String)
        Await Task.Delay(30)
        Return "DISPATCHED"
    End Function

    Public Async Function AggregateOrderAsync(orderId As String) As Task(Of OrderSummary)
        Dim customerTask = GetCustomerNameAsync(orderId)
        Dim totalTask = GetOrderTotalAsync(orderId)
        Dim shippingTask = GetShippingStatusAsync(orderId)

        Await Task.WhenAll(customerTask, totalTask, shippingTask)

        Return New OrderSummary With {
            .OrderId = orderId,
            .CustomerName = customerTask.Result,
            .TotalAmount = totalTask.Result,
            .Status = shippingTask.Result
        }
    End Function

    Public Async Function Main() As Task
        Dim summary = Await AggregateOrderAsync("ORD-9801")
        Console.WriteLine($"Order: {summary.OrderId} | Customer: {summary.CustomerName} | Total: {summary.TotalAmount:C2} | Status: {summary.Status}")
    End Function
End Module
```

Output:

```text
Order: ORD-9801 | Customer: Alice Smith | Total: $349.95 | Status: DISPATCHED
```

---

# Summary

| Construct | VB.NET Syntax | Purpose |
| :--- | :--- | :--- |
| **`Async Function ... As Task`** | Non-value returning async procedure | Standard async method declaration |
| **`Async Function ... As Task(Of T)`** | Value-returning async procedure | Returns a typed task result |
| **`Await`** | `Await task` | Asynchronously awaits completion |
| **`Task.WhenAll`** | `Await Task.WhenAll(t1, t2)` | Runs tasks in parallel; waits for all |
| **`Task.WhenAny`** | `Await Task.WhenAny(t1, t2)` | Returns first completed task |
| **`Task.Delay`** | `Await Task.Delay(ms)` | Non-blocking asynchronous pause |
| **Exception Filter**| `Catch ex As Exception When cond` | Conditionally handles exceptions |
