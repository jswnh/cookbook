## 1. Core Built-in PHP & SPL Collections

PHP utilizes a single unified `array` type that acts as an ordered list, vector, hash table, stack, and queue. For advanced usage, the Standard PHP Library (SPL) contains optimized data structures.

### Linear & Contiguous Collections

| Data Structure         | Underlying Structure | Primary Use Case                                                  | Time Complexity (Access / Insert)             | Example                                                       |
| :--------------------- | :------------------- | :---------------------------------------------------------------- | :-------------------------------------------- | :------------------------------------------------------------ |
| **`array` (Indexed)**  | Hash Map + Link List | Resizable, sequential index-based data storage (copy-on-write)     | $O(1)$ by index / $O(1)$ amortized append     | `$weekDays = ["Mon", "Tue", "Wed"];`                          |
| **`SplFixedArray`**    | Contiguous raw array | Memory-efficient fixed-size arrays (faster and uses less RAM)     | $O(1)$ by index / N/A (Fixed size)            | `$buffer = new SplFixedArray(10);`                            |
| **`SplDoublyLinkedList`**| Doubly-linked list | Fast insertions/removals in the middle of lists                   | $O(n)$ access / $O(1)$ insert (if node known) | `$history = new SplDoublyLinkedList();`                       |

### Key-Value & Set Collections

| Data Structure         | Underlying Structure   | Primary Use Case                                                         | Time Complexity (Search / Insert) | Example                                                  |
| :--------------------- | :--------------------- | :----------------------------------------------------------------------- | :-------------------------------- | :------------------------------------------------------- |
| **`array` (Associative)**| Hash Map             | High-speed lookups mapping keys (strings or integers) to values          | $O(1)$ average / $O(1)$ average   | `$userCache = ["id1" => $userObj];`                      |
| **`SplObjectStorage`** | Hash Map of objects    | Storing unique object instances (acts as a Set for objects)              | $O(1)$ average / $O(1)$ average   | `$visited = new SplObjectStorage();`                     |
| **Array keys Set**     | Hash Map               | Simulating Sets for strings/integers by using keys (due to key lookup O(1))| $O(1)$ average / $O(1)$ average   | `$uniqueEmails = ["a@b.com" => true];`                   |

### Specialized Linear Collections (SPL)

| Data Structure         | Underlying Structure | Primary Use Case                                                     | Operations               | Example                                              |
| :--------------------- | :------------------- | :------------------------------------------------------------------- | :----------------------- | :--------------------------------------------------- |
| **`SplQueue`**         | Doubly-linked list   | First-In, First-Out (FIFO) queue processing                          | `enqueue()`, `dequeue()` | `$queue = new SplQueue();`                           |
| **`SplStack`**         | Doubly-linked list   | Last-In, First-Out (LIFO) stack tracking                             | `push()`, `pop()`        | `$stack = new SplStack();`                           |
| **`SplPriorityQueue`**  | Max-Heap             | Processing elements based on priority level, not insertion order     | `insert()`, `extract()`  | `$pq = new SplPriorityQueue();`                      |

---

## 2. Memory-Optimized & Pointer-like Collections

PHP manages memory using copy-on-write and ref-counting. True memory pointer manipulation is not supported, but optimized memory layouts are available.

| Structure               | Nature                          | Primary Use Case                                                                                            | Example                                                         |
| :---------------------- | :------------------------------ | :---------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------- |
| **`SplFixedArray`**     | Flat C-style array              | Drastically lowers memory usage compared to standard PHP arrays by skipping hash-table tracking.           | `$fixed = new SplFixedArray(1000);`                             |
| **`WeakReference`**     | Weak pointer reference          | Referencing objects without incrementing their reference count, allowing garbage collection to destroy them. | `$weakRef = WeakReference::create($obj);`                       |
| **`WeakMap` (PHP 8.0+)**| Weakly-held hash table          | Storing metadata maps keyed by objects. Keys are GCed when no other references exist, preventing leaks.     | `$map = new WeakMap(); $map[$user] = "meta";`                   |

---

## 3. Concurrency and Shared-Memory Structures

PHP traditionally runs in isolated processes. For shared-state concurrency, extensions or external memory stores are utilized.

| Structure Group / Type | Underlying Architecture     | Primary Use Case                                                                      | Example                                                                 |
| :--------------------- | :-------------------------- | :------------------------------------------------------------------------------------ | :---------------------------------------------------------------------- |
| **`Shmop` extension**  | Shared memory segments      | Reading/writing directly to shared memory blocks on the OS level                      | `$shm_id = shmop_open(0xff3, "c", 0644, 100);`                          |
| **Swoole Table**       | Lock-free shared memory map | Fast multi-process sharing of key-value data in Swoole async servers                  | `$table = new Swoole\Table(1024);`                                      |
| **APCu Cache**         | Shared APC memory segment   | In-memory caching shared across all PHP-FPM requests                                  | `apcu_store('key', $value);`                                            |

---

## 4. Modern Read-Only and Record-Based Structures

Modern PHP (8.1+) includes powerful type tools to implement immutable records and simple value objects.

| Keyword / Type          | Nature                  | Primary Use Case                                                                                       | Example                                                       |
| :---------------------- | :---------------------- | :----------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| **`readonly class`**    | Immutable Reference     | Declares class where all properties are automatically readonly (immutable after constructor)           | `readonly class Point { public function __construct(public float $x, public float $y) {} }` |
| **`readonly` property** | Immutable Class Field   | Allows setting property value only once during initialization (PHP 8.1+).                              | `public readonly string $uuid;`                               |
| **`stdClass`**          | Generic Object          | Dynamic, anonymous schema object (similar to JS plain objects).                                        | `$data = (object) ['name' => 'John'];`                        |
| **`array` tuple**       | Destructurable array    | Inline return structure mimicking tuples.                                                              | `[$lat, $lng] = [40.71, -74.00];`                             |
