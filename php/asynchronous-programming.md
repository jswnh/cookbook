# 1. Synchronous vs Asynchronous Execution

## Synchronous Execution

Operations block the executing thread/process.

```php
$data1 = file_get_contents("https://api.example.com/users");
$data2 = file_get_contents("https://api.example.com/orders");
```

---

## Asynchronous Execution

Using Fibers or Event Loops, the thread yields execution during I/O delays to perform other tasks.

```php
// ReactPHP Async Example
use React\Http\Browser;
use function React\Async\await;

$browser = new Browser();

$promise1 = $browser->get("https://api.example.com/users");
$promise2 = $browser->get("https://api.example.com/orders");

// Await completion concurrently
[$usersResponse, $ordersResponse] = await(\React\Promise\all([$promise1, $promise2]));
```

---

# 2. Fibers (PHP 8.1+ Native Coroutines)

Fibers represent cooperative, full-stack coroutines. They allow code to suspend its execution block, yielding control back to the calling scope, and be resumed from the exact point of suspension.

## Creating and Using a Fiber

```php
$fiber = new Fiber(function (): void {
    echo "Fiber started\n";

    // Suspend execution and send a value to the caller
    $valuePassedBack = Fiber::suspend("Value from Fiber");

    echo "Fiber resumed with: {$valuePassedBack}\n";
});

// Start the fiber
$valueFromFiber = $fiber->start();
echo "Main scope got: {$valueFromFiber}\n";

// Resume the fiber and pass a value back into it
$fiber->resume("Main Scope Data");
```

Output:

```text
Fiber started
Main scope got: Value from Fiber
Fiber resumed with: Main Scope Data
```

_Note: Fibers do not run in parallel; they provide cooperative concurrency on a single thread._

---

# 3. Promises and Event Loops (ReactPHP & Amp)

PHP's async ecosystem relies on third-party event loops to scheduler non-blocking I/O.

## ReactPHP Event Loop & Promises

```php
require 'vendor/autoload.php';

use React\EventLoop\Loop;

Loop::addTimer(1.0, function () {
    echo "Timer fired after 1 second\n";
});

echo "Starting loop...\n";
Loop::run();
```

---

# 4. Concurrent Execution (`WhenAll` Equivalent)

Running multiple network or filesystem operations simultaneously.

### Using Amp Futures

```php
use Amp\Future;
use function Amp\async;

$future1 = async(fn() => longRunningTask("A"));
$future2 = async(fn() => longRunningTask("B"));

// Await all concurrent tasks
$results = Future\all([$future1, $future2]);
```

### Using ReactPHP Promises

```php
use function React\Async\await;
use function React\Promise\all;

$promise1 = fetchUrl("https://api.a.com");
$promise2 = fetchUrl("https://api.b.com");

$responses = await(all([$promise1, $promise2]));
```

---

# 5. Exception Handling

Exceptions thrown inside Fibers or async tasks propagate to the calling/awaiting scope.

```php
use React\Async;

try {
    $result = Async\await(somePromise());
} catch (\Throwable $exception) {
    echo "Caught: " . $exception->getMessage() . "\n";
}
```

---

# 6. Cancellation

Cooperative cancellation is handled using cancellation tokens (such as `Amp\Cancellation`).

```php
use Amp\DeferredCancellation;
use function Amp\delay;

$deferredCancellation = new DeferredCancellation();
$token = $deferredCancellation->getToken();

// Cancel after 2 seconds
Loop::addTimer(2.0, fn() => $deferredCancellation->cancel());

try {
    // Pass the token to an async operation
    delay(5.0, cancellation: $token);
} catch (\Amp\CancelledException $e) {
    echo "Operation cancelled.\n";
}
```

---

# 7. Asynchronous Generators

You can write async generators by yielding values inside a coroutine context.

```php
function generateNumbersAsync(): Generator {
    for ($i = 1; $i <= 5; $i++) {
        // Yield execution, suspending the generator
        yield $i;
    }
}

foreach (generateNumbersAsync() as $number) {
    echo $number . "\n";
}
```

---

# 8. Async Best Practices in PHP

## Never use blocking functions in an Event Loop

Avoid using `sleep()`, `file_get_contents()`, `pdo->query()`, or any blocking library inside a ReactPHP or Amp project. Instead, use non-blocking equivalents like `React\Http\Browser`, `Amp\Mysql`, or ReactPHP's filesystem wrapper.

---

## Prefer Fibers for synchronous-like flow

Fibers enable writing asynchronous code that looks and behaves like synchronous code without callback nesting.

---

# Summary

Core building blocks:

| Concept             | PHP / Ecosystem Equivalent          | Purpose                               |
| ------------------- | ----------------------------------- | ------------------------------------- |
| `Task` / `Promise`  | `React\Promise\PromiseInterface`    | Deferred placeholder for async result |
| `Fiber`             | Native `Fiber` class (PHP 8.1+)     | Suspendable, local execution contexts |
| `async` / `await`   | `fn() => async(...)` / `await(...)` | Synchronous-looking async structure   |
| `Task.WhenAll`      | `Promise\all()` / `Future\all()`    | Concurrently execute multiple tasks   |
| `CancellationToken` | `Amp\Cancellation`                  | Cooperative task cancellation         |
| `Event Loop`        | `React\EventLoop` / `Amp\Loop`      | Engine scheduling non-blocking I/O    |

Asynchronous PHP is highly functional when paired with native **Fibers** or libraries like **ReactPHP** and **Amp**, allowing single-process PHP applications to execute fast, non-blocking network streams.
