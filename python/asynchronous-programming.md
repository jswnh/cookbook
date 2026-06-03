# 1. Synchronous vs Asynchronous Execution

## Synchronous Execution

Operations execute sequentially. Each task must complete before the next begins.

```python
data = repository.get_users()
print(len(data))
```

Execution flow:

```text
get_users()
   ↓
Wait until complete
   ↓
Continue execution
```

The calling thread remains blocked until the operation finishes.

---

## Asynchronous Execution

Operations can pause execution without blocking the event loop.

```python
data = await repository.get_users_async()
print(len(data))
```

Execution flow:

```text
get_users_async()
   ↓
Returns coroutine
   ↓
Event loop released
   ↓
Operation completes
   ↓
Execution resumes
```

---

# 2. Coroutines

The foundation of async programming in Python is the coroutine (defined with `async def`).

## Coroutine without return value

```python
import asyncio

async def send_email() -> None:
    await asyncio.sleep(1)
    print("Email sent.")
```

Usage:

```python
await send_email()
```

---

## Coroutine with return value

```python
async def get_username() -> str:
    await asyncio.sleep(1)
    return "John"
```

Usage:

```python
username: str = await get_username()
```

---

# 3. async and await

These keywords provide a clean syntax for asynchronous code.

## async def

Marks a function as a coroutine.

```python
async def process() -> None:
    pass
```

---

## await

Suspends execution until the awaited coroutine completes.

```python
await asyncio.sleep(1)
```

Without `await`:

```python
asyncio.sleep(1)   # Creates coroutine object but never runs it

print("Runs immediately")
```

With `await`:

```python
await asyncio.sleep(1)

print("Runs after delay")
```

---

# 4. Return Types for Async Functions

## No return value

```python
async def save() -> None:
    await asyncio.sleep(0.5)
```

---

## Returns a value

```python
async def get_count() -> int:
    await asyncio.sleep(0.5)
    return 100
```

---

## Synchronous / cached result (analogous to `ValueTask<T>`)

```python
async def get_cached_value() -> str:
    return "Cached"   # Returns immediately without yielding
```

---

# 5. Concurrent Execution

Multiple asynchronous operations can run simultaneously.

## Sequential

```python
await get_users_async()
await get_orders_async()
await get_products_async()
```

Total time:

```text
1s + 1s + 1s = 3s
```

---

## Concurrent

```python
await asyncio.gather(
    get_users_async(),
    get_orders_async(),
    get_products_async(),
)
```

Total time:

```text
≈ 1 second
```

---

# 6. asyncio.gather

Waits for all coroutines to complete (analogous to `Task.WhenAll`).

```python
users, orders = await asyncio.gather(
    user_repository.get_async(),
    order_repository.get_async(),
)
```

Benefits:

- Faster execution
- Better resource utilization
- Reduced overall latency

---

# 7. asyncio.wait — First Completed

Returns when the first task completes (analogous to `Task.WhenAny`).

```python
import asyncio

task1 = asyncio.create_task(download_file_async())
task2 = asyncio.create_task(download_file_async())

done, pending = await asyncio.wait(
    {task1, task2},
    return_when=asyncio.FIRST_COMPLETED,
)
```

Common use cases:

- Timeout handling
- Race conditions
- Fastest-response wins

---

# 8. Exception Handling

Exceptions propagate through awaited coroutines.

```python
try:
    await process_order_async()
except Exception as ex:
    print(ex)
```

---

## Multiple Exceptions

```python
try:
    await asyncio.gather(
        service_a_async(),
        service_b_async(),
    )
except Exception as ex:
    print(ex)
```

Collect all errors using `return_exceptions=True`:

```python
results = await asyncio.gather(
    service_a_async(),
    service_b_async(),
    return_exceptions=True,
)
```

---

# 9. Cancellation

Allows cooperative cancellation of asynchronous operations.

```python
async def process() -> None:
    try:
        await asyncio.sleep(10)
    except asyncio.CancelledError:
        print("Task was cancelled")
        raise
```

Usage:

```python
task = asyncio.create_task(process())

await asyncio.sleep(5)
task.cancel()

try:
    await task
except asyncio.CancelledError:
    pass
```

Benefits:

- Prevents wasted work
- Supports request timeouts
- Improves scalability

---

# 10. Async Generators

Supports asynchronous streaming (analogous to `IAsyncEnumerable<T>`).

Instead of waiting for the entire collection:

```python
list[User]
```

You can stream items one at a time:

```python
AsyncGenerator[int, None]
```

Example:

```python
from typing import AsyncGenerator

async def generate_numbers() -> AsyncGenerator[int, None]:
    for i in range(1, 6):
        await asyncio.sleep(1)
        yield i
```

Consumption:

```python
async for number in generate_numbers():
    print(number)
```

Benefits:

- Lower memory usage
- Faster first results
- Better handling of large datasets

---

# 11. asyncio.timeout (Python 3.11+)

Controls operation timeout (analogous to `CancellationToken` with deadline).

```python
async def fetch_data() -> None:
    async with asyncio.timeout(5.0):
        await long_running_operation()
```

Or using `asyncio.wait_for`:

```python
try:
    result = await asyncio.wait_for(
        get_users_async(),
        timeout=5.0,
    )
except asyncio.TimeoutError:
    print("Operation timed out")
```

---

# 12. Async Best Practices

## Prefer Async All the Way

Avoid mixing sync and async improperly. Use `await` inside async functions.

Use:

```python
result = await get_users_async()
```

---

## Avoid Blocking the Event Loop

Bad:

```python
import time

async def bad_sleep() -> None:
    time.sleep(1)   # Blocks the entire event loop!
```

Good:

```python
async def good_sleep() -> None:
    await asyncio.sleep(1)
```

For CPU-bound work, offload to a thread pool:

```python
import asyncio

result = await asyncio.to_thread(cpu_bound_function, arg)
```

---

## Use asyncio.create_task for Fire-and-Forget

```python
task = asyncio.create_task(background_job())
```

---

## Use Meaningful Async Naming

```python
async def get_users_async() -> list[User]: ...
async def save_order_async(order: Order) -> None: ...
async def delete_user_async(user_id: int) -> None: ...
```

---

# 13. Modern Example (Python 3.13+)

```python
from dataclasses import dataclass
from typing import Optional
import asyncio


@dataclass
class User:
    id: int
    name: str


@dataclass
class UserDto:
    id: int
    name: str


class UserService:
    def __init__(self, repository: "UserRepository") -> None:
        self._repository = repository

    async def get_user_async(
        self,
        user_id: int,
    ) -> Optional[UserDto]:
        user = await self._repository.get_by_id_async(user_id)

        if user is None:
            return None

        return UserDto(id=user.id, name=user.name)
```

---

# Summary

Core building blocks:

| Concept                  | Purpose                             |
| ------------------------ | ----------------------------------- |
| `async def`              | Defines a coroutine                 |
| `await`                  | Waits without blocking event loop   |
| `asyncio.gather()`       | Execute multiple tasks concurrently |
| `asyncio.wait()`         | Wait for first completion           |
| `asyncio.CancelledError` | Cancel operations cooperatively     |
| `asyncio.timeout()`      | Enforce time limits (Python 3.11+)  |
| `async for`              | Iterate async generators            |
| `asyncio.create_task()`  | Schedule coroutine concurrently     |
| `asyncio.to_thread()`    | Run blocking code off event loop    |

Modern Python asynchronous programming is built around `async def`, `await`, and the `asyncio` event loop, enabling scalable, responsive, and high-performance applications across web services, data pipelines, and distributed systems.
