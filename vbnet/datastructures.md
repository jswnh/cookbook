## 1. Core Built-in Collections (`System.Collections.Generic`)

Target Environment: **Modern VB.NET (.NET 8 / .NET 9 / .NET 10 - Visual Basic 16.9+)**

### Linear & Contiguous Collections

| Data Structure | Underlying Structure | Primary Use Case | Time Complexity (Access / Insert) | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`T()` (Array)** | Fixed-size Contiguous Block | Lowest overhead, fixed-size indexed buffers | $O(1)$ index / N/A (fixed) | `Dim arr() As Integer = {1, 2, 3}` |
| **`List(Of T)`** | Dynamic Resizable Array | Default choice for indexed, sequential lists | $O(1)$ index / $O(1)$ amortized append | `Dim list As New List(Of String)()` |
| **`LinkedList(Of T)`** | Doubly-Linked List | High-frequency insertions/removals in the middle of a chain | $O(n)$ index / $O(1)$ insert at node | `Dim history As New LinkedList(Of String)()` |

---

### Key-Value & Set Collections

| Data Structure | Underlying Structure | Ordering | Time Complexity (Search / Insert) | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`Dictionary(Of K, V)`**| Hash Table | None | $O(1)$ average | `Dim map As New Dictionary(Of String, Integer)()` |
| **`HashSet(Of T)`** | Hash Table (Keys only) | None | $O(1)$ average | `Dim set As New HashSet(Of String)()` |
| **`SortedDictionary(Of K, V)`**| Red-Black Tree | Sorted by Key | $O(\log n)$ | `Dim sortedMap As New SortedDictionary(Of String, String)()` |
| **`SortedSet(Of T)`** | Red-Black Tree | Sorted Order | $O(\log n)$ | `Dim sortedSet As New SortedSet(Of Integer)()` |

---

### Specialized Queue & Stack Collections

| Data Structure | Underlying Structure | Primary Use Case | Operations | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`Queue(Of T)`** | Circular Array | First-In, First-Out (FIFO) pipeline | `Enqueue()`, `Dequeue()` $O(1)$ | `Dim q As New Queue(Of String)()` |
| **`Stack(Of T)`** | Dynamic Array | Last-In, First-Out (LIFO) tracking | `Push()`, `Pop()` $O(1)$ | `Dim s As New Stack(Of String)()` |
| **`PriorityQueue(Of TElement, TPriority)`**| Binary Heap | Element processing ordered strictly by priority rank | `Enqueue()`, `Dequeue()` $O(\log n)$ | `Dim pq As New PriorityQueue(Of String, Integer)()` |

```vb
Dim taskQueue As New PriorityQueue(Of String, Integer)()
taskQueue.Enqueue("Low priority task", 3)
taskQueue.Enqueue("Emergency bugfix", 1)
taskQueue.Enqueue("Feature release", 2)

While taskQueue.Count > 0
    Console.WriteLine(taskQueue.Dequeue())
End While
' Output: Emergency bugfix, Feature release, Low priority task
```

---

## 2. High-Performance Concurrent Collections

Designed for safe multi-threaded workflows without manual locking (`System.Collections.Concurrent`).

| Data Structure | Architecture | Primary Use Case | Example |
| :--- | :--- | :--- | :--- |
| **`ConcurrentDictionary(Of K, V)`** | Fine-grained lock striping | Multi-threaded read/write key-value caching | `Dim cache As New ConcurrentDictionary(Of String, User)()` |
| **`ConcurrentQueue(Of T)`** | Lock-free linked segments | Multi-producer / multi-consumer thread streams | `Dim q As New ConcurrentQueue(Of TaskItem)()` |
| **`ConcurrentStack(Of T)`** | Lock-free Treiber stack | Multi-threaded LIFO processing | `Dim s As New ConcurrentStack(Of LogEntry)()` |
| **`ConcurrentBag(Of T)`** | Thread-local storage pools | Mixed unordered item storage with thread affinity | `Dim pool As New ConcurrentBag(Of ClientConnection)()` |
| **`BlockingCollection(Of T)`** | Thread synchronization wrapper | Producer-consumer pipelines with bounded limits | `Dim bc As New BlockingCollection(Of Byte())(boundedCapacity:=100)` |

---

## 3. Read-Only and Immutable Collections

```vb
Imports System.Collections.Generic
Imports System.Collections.Immutable

' Read-Only wrapper view around a mutable list
Dim internalList As New List(Of String) From {"A", "B"}
Dim readOnlyWrapper As IReadOnlyList(Of String) = internalList.AsReadOnly()

' Immutable List (creates new structural instances on modification)
Dim immutable As ImmutableList(Of String) = ImmutableList.Create("Alpha", "Beta")
Dim updated = immutable.Add("Gamma")
```

---

## 4. ValueTuples

Modern VB.NET supports lightweight, value-based tuples that avoid heap allocations.

```vb
Dim point As (X As Integer, Y As Integer) = (100, 200)
Console.WriteLine($"Point coordinates: ({point.X}, {point.Y})")

Dim status As (Code As Integer, Message As String) = (200, "OK")
Console.WriteLine($"Result: {status.Code} - {status.Message}")
```

---

# Summary

| Data Structure | Best Choice When |
| :--- | :--- |
| **`T()`** | You know the fixed size in advance and need zero overhead |
| **`List(Of T)`** | You need a sequential list with frequent additions and indexed lookups |
| **`Dictionary(Of K, V)`** | You need fast associative lookups by key |
| **`HashSet(Of T)`** | You need to ensure distinct values and fast membership checking |
| **`PriorityQueue(Of T, P)`** | Tasks must be processed in prioritized order |
| **`ConcurrentDictionary`**| Multiple threads concurrently read and write key-value pairs |
