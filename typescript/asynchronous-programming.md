# 1. Synchronous vs Asynchronous Execution

## Synchronous Execution

Operations execute sequentially. Each task must complete before the next begins.

```typescript
const data = repository.getUsers();
console.log(data.length);
```

Execution flow:

```text
getUsers()
   ↓
Wait until complete
   ↓
Continue execution
```

The executing thread remains blocked until the operation finishes.

---

## Asynchronous Execution

Operations run concurrently, registering callbacks or returning promises without blocking the event loop.

```typescript
const data = await repository.getUsersAsync();
console.log(data.length);
```

Execution flow:

```text
getUsersAsync()
   ↓
Returns Promise
   ↓
Thread released
   ↓
Operation completes
   ↓
Execution resumes
```

---

# 2. Promise and Promise<T>

The foundation of asynchronous programming in TypeScript is the `Promise` type.

## Promise (Promise<void>)

Represents an asynchronous operation that does not return a value.

```typescript
async function sendEmailAsync(): Promise<void> {
  await new Promise((resolve) => setTimeout(resolve, 1000));
  console.log("Email sent.");
}
```

Usage:

```typescript
await sendEmailAsync();
```

---

## Promise<T>

Represents an asynchronous operation that returns a value.

```typescript
async function getUsernameAsync(): Promise<string> {
  await new Promise((resolve) => setTimeout(resolve, 1000));
  return "John";
}
```

Usage:

```typescript
const username = await getUsernameAsync();
```

---

# 3. async and await

These keywords provide a clean syntax for asynchronous code.

## async

Marks a function as asynchronous, forcing it to return a Promise.

```typescript
async function processAsync(): Promise<void> {}
```

---

## await

Suspends execution until the awaited Promise resolves or rejects.

```typescript
await new Promise((resolve) => setTimeout(resolve, 1000));
```

Without `await`:

```typescript
delay(1000); // Returns Promise object immediately but doesn't halt execution

console.log("Runs immediately");
```

With `await`:

```typescript
await delay(1000);

console.log("Runs after delay");
```

---

# 4. Return Types for Async Methods

## Promise<void>

No return value.

```typescript
async function saveAsync(): Promise<void> {
  await delay(500);
}
```

---

## Promise<T>

Returns a value.

```typescript
async function getCountAsync(): Promise<number> {
  await delay(500);
  return 100;
}
```

---

# 5. Concurrent Execution

Multiple asynchronous operations can run simultaneously.

## Sequential

```typescript
await getUsersAsync();
await getOrdersAsync();
await getProductsAsync();
```

Total time:

```text
1s + 1s + 1s = 3s
```

---

## Concurrent

```typescript
await Promise.all([getUsersAsync(), getOrdersAsync(), getProductsAsync()]);
```

Total time:

```text
≈ 1 second
```

---

# 6. Promise.all

Waits for all Promises to resolve. If any promise rejects, the entire aggregate rejects immediately.

```typescript
const userPromise = userRepository.getAsync();
const orderPromise = orderRepository.getAsync();

await Promise.all([userPromise, orderPromise]);

const users = await userPromise;
const orders = await orderPromise;
```

Benefits:

- Faster execution
- Better resource utilization
- Reduced overall latency

---

# 7. Promise.race / Promise.any

## Promise.race

Returns as soon as the first promise completes (either resolves or rejects).

```typescript
const task1 = downloadFileAsync();
const task2 = downloadFileAsync();

const fastestResult = await Promise.race([task1, task2]);
```

## Promise.any

Returns as soon as the first promise resolves successfully. Rejects only if _all_ promises fail.

```typescript
const fastestSuccess = await Promise.any([task1, task2]);
```

Common use cases:

- Timeout handling
- Race conditions
- Fastest-response wins

---

# 8. Exception Handling

Exceptions propagate through awaited promises and can be caught using standard try-catch blocks.

```typescript
try {
  await processOrderAsync();
} catch (ex: any) {
  console.error(ex.message);
}
```

---

## Multiple Exceptions (Promise.allSettled)

Unlike `Promise.all`, `Promise.allSettled` waits for all operations to complete (regardless of success or failure), returning status descriptors for each.

```typescript
const results = await Promise.allSettled([serviceAAsync(), serviceBAsync()]);

results.forEach((result) => {
  if (result.status === "fulfilled") {
    console.log("Success:", result.value);
  } else {
    console.error("Failed:", result.reason);
  }
});
```

---

# 9. AbortController / AbortSignal

Allows cooperative cancellation of asynchronous operations (analogous to `CancellationToken` in .NET).

```typescript
async function processAsync(signal: AbortSignal): Promise<void> {
  if (signal.aborted) {
    throw new DOMException("Aborted", "AbortError");
  }

  // Pass the signal down to other async APIs (like fetch)
  const response = await fetch("https://api.example.com/data", { signal });
  const data = await response.json();
}
```

Usage:

```typescript
const controller = new AbortController();

// Cancel the operation after 5 seconds
setTimeout(() => controller.abort(), 5000);

try {
  await processAsync(controller.signal);
} catch (err: any) {
  if (err.name === "AbortError") {
    console.log("Operation was cancelled.");
  }
}
```

---

# 10. Async Generators (AsyncIterable)

Supports asynchronous streaming (analogous to `IAsyncEnumerable<T>`).

Instead of waiting for the entire collection:

```typescript
Promise<User[]>;
```

You can stream items one at a time:

```typescript
AsyncGenerator<number>;
```

Example:

```typescript
async function* generateNumbersAsync(): AsyncGenerator<number> {
  for (let i = 1; i <= 5; i++) {
    await new Promise((resolve) => setTimeout(resolve, 1000));
    yield i;
  }
}
```

Consumption:

```typescript
for await (const number of generateNumbersAsync()) {
  console.log(number);
}
```

Benefits:

- Lower memory usage
- Faster first results
- Better handling of large datasets

---

# 11. ConfigureAwait Note

In JavaScript/TypeScript runtimes (V8, JavaScriptCore, etc.), there is no multithreaded SynchronizationContext or thread pool hopping. All execution happens on a single thread via the event loop microtask queue. As a result, C#'s `ConfigureAwait` pattern does not exist and is unnecessary.

---

# 12. Async Best Practices

## Prefer Async All the Way

Avoid wrapping async code in synchronous blocks or omitting `await` when returning async workflows within try-catch blocks (omitting `await` will leak the promise rejection past the local try-catch boundary).

```typescript
// Good
async function getResult() {
  try {
    return await fetchSomethingAsync();
  } catch (e) {
    return "default";
  }
}
```

---

## Avoid Thread Blocking

Do not run CPU-intensive synchronous operations on the main thread. For heavy computations, offload to web workers (browsers) or worker threads (Node.js).

---

## Pass AbortSignal

Ensure long-running or network-bound async functions accept and forward an `AbortSignal` to support timeouts and cancellations.

---

# 13. Modern TypeScript Service Example

```typescript
export interface User {
  id: string;
  name: string;
}

export interface UserDto {
  id: string;
  name: string;
}

export interface UserRepository {
  getByIdAsync(id: string, signal?: AbortSignal): Promise<User | null>;
}

export class UserService {
  constructor(private readonly repository: UserRepository) {}

  public async getUserAsync(
    userId: string,
    signal?: AbortSignal,
  ): Promise<UserDto | null> {
    const user = await this.repository.getByIdAsync(userId, signal);

    if (!user) {
      return null;
    }

    return {
      id: user.id,
      name: user.name,
    };
  }
}
```

---

# Summary

Core building blocks:

| Concept                    | Purpose                                |
| -------------------------- | -------------------------------------- |
| `Promise<T>`               | Asynchronous operation                 |
| `async`                    | Marks an asynchronous function         |
| `await`                    | Waits without blocking the thread      |
| `Promise.all()`            | Execute multiple promises concurrently |
| `Promise.race()` / `any()` | Wait for first completion              |
| `AbortController`          | Cancel operations cooperatively        |
| `AsyncGenerator<T>`        | Stream data asynchronously             |
| `for await...of`           | Consume async iterators                |

Modern TypeScript asynchronous programming revolves around `Promise`, `async`, and `await`, backed by the runtime event loop. By using modern APIs like `AbortController` and `AsyncGenerators`, developers can build extremely scalable and highly responsive cross-platform systems.
