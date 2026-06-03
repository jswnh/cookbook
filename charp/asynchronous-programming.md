# 1. Synchronous vs Asynchronous Execution

## Synchronous Execution

Operations execute sequentially. Each task must complete before the next begins.

```csharp
var data = repository.GetUsers();
Console.WriteLine(data.Count);
```

Execution flow:

```text
GetUsers()
   ↓
Wait until complete
   ↓
Continue execution
```

The calling thread remains blocked until the operation finishes.

---

## Asynchronous Execution

Operations can pause execution without blocking the thread.

```csharp
var data = await repository.GetUsersAsync();
Console.WriteLine(data.Count);
```

Execution flow:

```text
GetUsersAsync()
   ↓
Returns Task
   ↓
Thread released
   ↓
Operation completes
   ↓
Execution resumes
```

---

# 2. Task and Task<T>

The foundation of asynchronous programming in .NET is the `Task` type.

## Task

Represents an asynchronous operation that does not return a value.

```csharp
public async Task SendEmailAsync()
{
    await Task.Delay(1000);

    Console.WriteLine("Email sent.");
}
```

Usage:

```csharp
await SendEmailAsync();
```

---

## Task<T>

Represents an asynchronous operation that returns a value.

```csharp
public async Task<string> GetUsernameAsync()
{
    await Task.Delay(1000);

    return "John";
}
```

Usage:

```csharp
string username = await GetUsernameAsync();
```

---

# 3. async and await

These keywords provide a clean syntax for asynchronous code.

## async

Marks a method as asynchronous.

```csharp
public async Task ProcessAsync()
{
}
```

---

## await

Suspends execution until the awaited task completes.

```csharp
await Task.Delay(1000);
```

Without `await`:

```csharp
Task.Delay(1000);

Console.WriteLine("Runs immediately");
```

With `await`:

```csharp
await Task.Delay(1000);

Console.WriteLine("Runs after delay");
```

---

# 4. Return Types for Async Methods

## Task

No return value.

```csharp
public async Task SaveAsync()
{
    await Task.Delay(500);
}
```

---

## Task<T>

Returns a value.

```csharp
public async Task<int> GetCountAsync()
{
    await Task.Delay(500);

    return 100;
}
```

---

## ValueTask<T>

Used when a result may already be available synchronously.

```csharp
public ValueTask<string> GetCachedValueAsync()
{
    return ValueTask.FromResult("Cached");
}
```

Useful for high-performance scenarios where allocations matter.

---

# 5. Concurrent Execution

Multiple asynchronous operations can run simultaneously.

## Sequential

```csharp
await GetUsersAsync();
await GetOrdersAsync();
await GetProductsAsync();
```

Total time:

```text
1s + 1s + 1s = 3s
```

---

## Concurrent

```csharp
var usersTask = GetUsersAsync();
var ordersTask = GetOrdersAsync();
var productsTask = GetProductsAsync();

await Task.WhenAll(
    usersTask,
    ordersTask,
    productsTask);
```

Total time:

```text
≈ 1 second
```

---

# 6. Task.WhenAll

Waits for all tasks to complete.

```csharp
var userTask = userRepository.GetAsync();
var orderTask = orderRepository.GetAsync();

await Task.WhenAll(userTask, orderTask);

var users = await userTask;
var orders = await orderTask;
```

Benefits:

- Faster execution
- Better resource utilization
- Reduced overall latency

---

# 7. Task.WhenAny

Returns when the first task completes.

```csharp
var task1 = DownloadFileAsync();
var task2 = DownloadFileAsync();

Task completedTask =
    await Task.WhenAny(task1, task2);
```

Common use cases:

- Timeout handling
- Race conditions
- Fastest-response wins

---

# 8. Exception Handling

Exceptions propagate through awaited tasks.

```csharp
try
{
    await ProcessOrderAsync();
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}
```

---

## Multiple Exceptions

```csharp
try
{
    await Task.WhenAll(
        ServiceAAsync(),
        ServiceBAsync());
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}
```

When multiple tasks fail, .NET aggregates the exceptions internally.

---

# 9. Cancellation Tokens

Allows cooperative cancellation of asynchronous operations.

```csharp
public async Task ProcessAsync(
    CancellationToken cancellationToken)
{
    await Task.Delay(
        TimeSpan.FromSeconds(10),
        cancellationToken);
}
```

Usage:

```csharp
var cts = new CancellationTokenSource();

cts.CancelAfter(TimeSpan.FromSeconds(5));

await ProcessAsync(cts.Token);
```

Benefits:

- Prevents wasted work
- Supports request timeouts
- Improves scalability

---

# 10. IAsyncEnumerable<T>

Supports asynchronous streaming.

Instead of waiting for the entire collection:

```csharp
Task<List<User>>
```

You can stream items one at a time:

```csharp
IAsyncEnumerable<User>
```

Example:

```csharp
public async IAsyncEnumerable<int> GenerateNumbersAsync()
{
    for (int i = 1; i <= 5; i++)
    {
        await Task.Delay(1000);

        yield return i;
    }
}
```

Consumption:

```csharp
await foreach (var number in GenerateNumbersAsync())
{
    Console.WriteLine(number);
}
```

Benefits:

- Lower memory usage
- Faster first results
- Better handling of large datasets

---

# 11. ConfigureAwait

Controls whether execution resumes on the captured synchronization context.

```csharp
await repository.GetUsersAsync()
    .ConfigureAwait(false);
```

Common guidance:

| Application Type          | Recommendation      |
| ------------------------- | ------------------- |
| ASP.NET Core              | Usually unnecessary |
| Class Libraries           | Often recommended   |
| Desktop UI (WPF/WinForms) | Use carefully       |

---

# 12. Async Best Practices

## Prefer Async All the Way

Avoid:

```csharp
var result =
    GetUsersAsync().Result;
```

or

```csharp
GetUsersAsync().Wait();
```

These can cause deadlocks and thread blocking.

Use:

```csharp
var result =
    await GetUsersAsync();
```

---

## Avoid async void

Bad:

```csharp
public async void SaveData()
{
}
```

Good:

```csharp
public async Task SaveDataAsync()
{
}
```

Exception:

```csharp
private async void Button_Click(
    object sender,
    EventArgs e)
{
}
```

Event handlers are the primary valid use case.

---

## Pass CancellationToken

Prefer:

```csharp
Task<User> GetUserAsync(
    int id,
    CancellationToken cancellationToken);
```

Instead of:

```csharp
Task<User> GetUserAsync(int id);
```

for long-running operations.

---

## Use Meaningful Async Suffixes

```csharp
GetUsersAsync()
SaveOrderAsync()
DeleteUserAsync()
```

This follows Microsoft's naming conventions.

---

# 13. Modern ASP.NET Core Example

```csharp
public sealed class UserService(
    IUserRepository repository)
{
    public async Task<UserDto?> GetUserAsync(
        Guid userId,
        CancellationToken cancellationToken = default)
    {
        var user =
            await repository.GetByIdAsync(
                userId,
                cancellationToken);

        if (user is null)
        {
            return null;
        }

        return new UserDto
        {
            Id = user.Id,
            Name = user.Name
        };
    }
}
```

---

# Summary

Core building blocks:

| Concept                 | Purpose                             |
| ----------------------- | ----------------------------------- |
| `Task`                  | Asynchronous operation              |
| `Task<T>`               | Async operation returning a value   |
| `async`                 | Marks an asynchronous method        |
| `await`                 | Waits without blocking a thread     |
| `Task.WhenAll()`        | Execute multiple tasks concurrently |
| `Task.WhenAny()`        | Wait for first completion           |
| `CancellationToken`     | Cancel operations cooperatively     |
| `ValueTask<T>`          | Reduce allocations in hot paths     |
| `IAsyncEnumerable<T>`   | Stream data asynchronously          |
| `ConfigureAwait(false)` | Control context capture             |

Modern C# asynchronous programming is built around `Task`, `async`, and `await`, enabling scalable, responsive, and high-performance applications across ASP.NET Core, desktop, cloud, and distributed systems.
