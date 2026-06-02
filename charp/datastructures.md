## 1. Core Built-in Arrays & System.Collections.Generic

These structures manage sequential data. Raw arrays allocate a single contiguous block of memory on the heap, while generic collections wrap these mechanics for type-safe, dynamic manipulation.

### Linear & Contiguous Collections

| Data Structure                | Underlying Structure | Primary Use Case                                                  | Time Complexity (Access / Insert)             | Example                                                       |
| :---------------------------- | :------------------- | :---------------------------------------------------------------- | :-------------------------------------------- | :------------------------------------------------------------ |
| **`T[]` (Single-Dim Array)**  | Contiguous Block     | Fixed-size, lowest overhead sequential storage of data or buffers | $O(1)$ by index / N/A (Fixed size)            | `string[] weekDays = ["Mon", "Tue", "Wed"];`                  |
| **`T[,]` (Multidimensional)** | Single Flat Block    | Fixed-size true matrix grid layout (Rows $\times$ Columns)        | $O(1)$ by index / N/A (Fixed size)            | `char[,] gameBoard = { {'X', 'O', 'X'}, {'O', 'X', 'O'} };`   |
| **`T[][]` (Jagged Array)**    | Array of Arrays      | Non-uniform data shapes where nested rows vary in size            | $O(1)$ by index / N/A (Fixed size)            | `byte[][] variablePackages = [ [0xAA], [0xBB, 0xCC, 0xDD] ];` |
| **`List<T>`**                 | Dynamic Array        | Default choice for a resizable, sequential index-based collection | $O(1)$ by index / $O(1)$ amortized append     | `List<Guid> sessionIds = [Guid.NewGuid(), Guid.NewGuid()];`   |
| **`LinkedList<T>`**           | Doubly-Linked List   | High-frequency insertions/deletions in the middle of a pipeline   | $O(n)$ access / $O(1)$ insert (if node known) | `LinkedList<string> pageHistory = new();`                     |

### Key-Value & Set Collections

| Data Structure                       | Underlying Structure   | Primary Use Case                                                         | Time Complexity (Search / Insert) | Example                                                  |
| :----------------------------------- | :--------------------- | :----------------------------------------------------------------------- | :-------------------------------- | :------------------------------------------------------- |
| **`Dictionary<TKey, TValue>`**       | Hash Table             | High-speed, associative lookups mapping unique keys to values            | $O(1)$ average / $O(1)$ average   | `var userCache = new Dictionary<string, User>();`        |
| **`HashSet<T>`**                     | Hash Table (Keys only) | High-performance deduplication, uniqueness checks, and mathematical sets | $O(1)$ average / $O(1)$ average   | `HashSet<string> uniqueEmails = ["a@b.com", "b@c.com"];` |
| **`SortedDictionary<TKey, TValue>`** | Red-Black Tree         | Key-value mapping that keeps keys consistently sorted                    | $O(\log n)$ / $O(\log n)$         | `var metadata = new SortedDictionary<string, string>();` |
| **`SortedSet<T>`**                   | Red-Black Tree         | A unique set that auto-sorts entries upon insertion                      | $O(\log n)$ / $O(\log n)$         | `SortedSet<DateTime> systemLogs = [DateTime.UtcNow];`    |

### Specialized Linear Collections

| Data Structure                           | Underlying Structure | Primary Use Case                                                     | Operations               | Example                                              |
| :--------------------------------------- | :------------------- | :------------------------------------------------------------------- | :----------------------- | :--------------------------------------------------- |
| **`Queue<T>`**                           | Circular Array       | First-In, First-Out (FIFO) processing loops (e.g., Background tasks) | `Enqueue()`, `Dequeue()` | `Queue<OrderRequest> orderQueue = new();`            |
| **`Stack<T>`**                           | Dynamic Array        | Last-In, First-Out (LIFO) tracking (e.g., State changes, navigation) | `Push()`, `Pop()`        | `Stack<NavigationRoute> backStack = new();`          |
| **`PriorityQueue<TElement, TPriority>`** | Binary Heap          | Processing elements based on strict priority rank, not entry order   | `Enqueue()`, `Dequeue()` | `var alertQueue = new PriorityQueue<string, int>();` |

---

## 2. Low-Allocation & Stack-Optimized Structures

Introduced and refined in modern C# versions to minimize Garbage Collector (GC) overhead by operating directly over contiguous memory windows (like native heap, stack blocks, or standard arrays).

| Structure               | Memory Nature                   | Primary Use Case                                                                                            | Example                                                         |
| :---------------------- | :------------------------------ | :---------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------- |
| **`Span<T>`**           | **Ref Struct** (Stack-only)     | High-performance mutable manipulation over raw arrays, native buffers, or `stackalloc`                      | `Span<byte> buffer = stackalloc byte[256];`                     |
| **`ReadOnlySpan<T>`**   | **Ref Struct** (Stack-only)     | Zero-allocation immutable parsing over memory targets (strings, payload slices)                             | `ReadOnlySpan<char> format = "JSON_DATA".AsSpan(0, 4);`         |
| **`Memory<T>`**         | **Value Type** (Can go to Heap) | Heap-allocatable equivalent of `Span<T>` capable of outliving the stack (crucial for async/await workflows) | `Memory<byte> dynamicBuffer = new byte[2048];`                  |
| **`ReadOnlyMemory<T>`** | **Value Type** (Can go to Heap) | Heap-allocatable, read-only wrapper representing a segmented data string or byte sequence                   | `ReadOnlyMemory<char> persistentText = "SystemLog".AsMemory();` |

---

## 3. High-Performance Segmented & Concurrent Collections

Designed for specialized enterprise optimizations, such as multi-threaded concurrency data paths, asynchronous networking pipelines, or eliminating LOH (Large Object Heap) fragmentation.

| Structure Group / Type                          | Underlying Architecture     | Primary Use Case                                                                      | Example                                                                 |
| :---------------------------------------------- | :-------------------------- | :------------------------------------------------------------------------------------ | :---------------------------------------------------------------------- |
| **`ConcurrentDictionary<TKey, TValue>`**        | Fine-grained striping locks | Multi-threaded read/write mappings without global locks                               | `var liveConnections = new ConcurrentDictionary<string, Connection>();` |
| **`ConcurrentQueue<T>` / `ConcurrentStack<T>`** | Lock-free segmented arrays  | High-throughput multi-producer/multi-consumer data streams                            | `ConcurrentQueue<LogMessage> asyncLogs = new();`                        |
| **`ConcurrentBag<T>`**                          | Thread-local work-stealing  | Mixed pool of unordered storage where threads work mostly on their own items          | `ConcurrentBag<ObjectPoolItem> globalPool = new();`                     |
| **`ArraySegment<T>`**                           | Reference array sub-window  | Reference wrapper framing a specific slice of a standard array without making a clone | `var slice = new ArraySegment<byte>(rawBytes, offset: 10, count: 50);`  |
| **`ReadOnlySequence<T>`**                       | Linked segments of memory   | High-performance network streams parsing scattered data frames non-contiguously       | Used directly within high-throughput `System.IO.Pipelines`.             |

---

## 4. Modern Read-Only and Record-Based Structures

| Keyword / Type              | Memory Nature           | Primary Use Case                                                                                       | Example                                                       |
| :-------------------------- | :---------------------- | :----------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| **`ReadOnlyCollection<T>`** | Reference Type          | Read-only structural wrapper safe to expose publicly from backend domain models                        | `var exposedList = internalList.AsReadOnly();`                |
| **`record struct`**         | Value Type              | Lightweight, structural object layouts featuring automatic immutability and property-equality matching | `public record struct GeoCoordinate(double Lat, double Lng);` |
| **`Tuple` / `ValueTuple`**  | Value Type (ValueTuple) | Direct, local, unnamed methods output encapsulation for returning multiple values on-the-fly           | `var payload = (StatusCode: 200, Content: "Success");`        |
