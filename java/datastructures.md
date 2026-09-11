## 1. Core Built-in Collections (Java Collections Framework)

These structures manage sequential and associative data in Java under `java.util`.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

### Linear & Contiguous Collections

| Data Structure | Underlying Structure | Primary Use Case | Time Complexity (Access / Insert) | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`ArrayList<E>`** | Resizable Array | Default choice for fast index-based access | $O(1)$ by index / $O(1)$ amortized append | `List<String> list = new ArrayList<>();` |
| **`LinkedList<E>`** | Doubly-Linked List | Frequent insertions/deletions at ends or during iterator traversal | $O(n)$ by index / $O(1)$ at head/tail | `List<Task> queue = new LinkedList<>();` |
| **`ArrayDeque<E>`** | Resizable Circular Array | High-performance Stacks (LIFO) and Queues (FIFO), faster than `Stack` and `LinkedList` | $O(1)$ push/pop at both ends | `Deque<String> stack = new ArrayDeque<>();` |
| **`Vector<E>`** | Synchronized Resizable Array | Legacy thread-safe array (prefer modern concurrent collections) | $O(1)$ by index / $O(1)$ append (synchronized) | `Vector<Integer> v = new Vector<>();` |

---

### Key-Value & Set Collections

| Data Structure | Underlying Structure | Ordering | Time Complexity (Search / Insert) | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`HashMap<K, V>`** | Hash Table (Buckets + Red-Black Trees) | None | $O(1)$ average | `Map<String, User> cache = new HashMap<>();` |
| **`LinkedHashMap<K, V>`** | Hash Table + Doubly-Linked List | Insertion or Access Order | $O(1)$ average | `Map<String, Page> lru = new LinkedHashMap<>();` |
| **`TreeMap<K, V>`** | Red-Black Tree | Natural order or `Comparator` | $O(\log n)$ | `Map<LocalDate, Double> daily = new TreeMap<>();` |
| **`HashSet<E>`** | Backed by `HashMap` | None | $O(1)$ average | `Set<String> unique = new HashSet<>();` |
| **`LinkedHashSet<E>`** | Backed by `LinkedHashMap` | Insertion Order | $O(1)$ average | `Set<String> ordered = new LinkedHashSet<>();` |
| **`TreeSet<E>`** | Backed by `TreeMap` | Sorted Order | $O(\log n)$ | `Set<Integer> sorted = new TreeSet<>();` |

---

### Specialized Queue & Priority Collections

| Data Structure | Underlying Structure | Primary Use Case | Operations | Example |
| :--- | :--- | :--- | :--- | :--- |
| **`PriorityQueue<E>`** | Binary Heap | Processing elements ranked by priority / natural order | `offer()` $O(\log n)$, `poll()` $O(\log n)$ | `PriorityQueue<Job> jobs = new PriorityQueue<>();` |
| **`ArrayDeque<E>`** | Circular Buffer | FIFO queue processing without linked-node allocation | `offer()`, `poll()`, `peek()` $O(1)$ | `Queue<Request> reqs = new ArrayDeque<>();` |

---

## 2. Sequenced Collections (Java 21 to Java 26)

Java standardizes sequenced access across lists, deques, and ordered sets via `SequencedCollection`, `SequencedSet`, and `SequencedMap`.

```java
import java.util.*;

List<String> list = new ArrayList<>(List.of("A", "B", "C"));

// Direct first and last access
String first = list.getFirst(); // A
String last = list.getLast();   // C

// Add at ends
list.addFirst("Start");
list.addLast("End");

// Reversed view without copying data
SequencedCollection<String> reversedView = list.reversed();
System.out.println(reversedView); // [End, C, B, A, Start]
```

---

## 3. Low-Allocation & Memory-Optimized Structures

Modern Java provides zero-copy and off-heap memory structures to minimize Garbage Collection overhead for high-throughput networking, media processing, and low-latency systems.

| Structure | Nature | Primary Use Case | Example |
| :--- | :--- | :--- | :--- |
| **`ByteBuffer`** | Direct / Non-Direct Buffer | Byte manipulation for I/O channels (`java.nio`) | `ByteBuffer buf = ByteBuffer.allocateDirect(4096);` |
| **`MemorySegment` (FFM API)** | Off-heap Contiguous Native Memory | Safe, zero-copy off-heap memory with bounded lifetime | `Arena arena = Arena.ofConfined();`<br>`MemorySegment seg = arena.allocate(1024);` |
| **`Arena` (FFM API)** | Memory Lifecycle Management | Deterministic off-heap memory reclamation | `try (Arena arena = Arena.ofConfined()) { ... }` |

---

## 4. High-Performance & Thread-Safe Collections

Designed for concurrent multi-threaded execution under `java.util.concurrent`.

| Data Structure | Underlying Architecture | Primary Use Case | Example |
| :--- | :--- | :--- | :--- |
| **`ConcurrentHashMap<K, V>`** | Lock-free reads, partitioned striping | High-throughput concurrent key-value operations | `ConcurrentHashMap<String, Session> active = new ConcurrentHashMap<>();` |
| **`CopyOnWriteArrayList<E>`** | Array clone on mutation | Read-heavy scenarios with rare writes (Listeners/Observers) | `List<EventListener> listeners = new CopyOnWriteArrayList<>();` |
| **`ArrayBlockingQueue<E>`** | Bounded Circular Array with ReentrantLock | Multi-producer / multi-consumer pipelines with fixed backpressure | `BlockingQueue<Task> queue = new ArrayBlockingQueue<>(500);` |
| **`LinkedBlockingQueue<E>`** | Bounded/Unbounded Linked Nodes | Work queues with independent put and take locks | `BlockingQueue<Event> queue = new LinkedBlockingQueue<>();` |
| **`ConcurrentLinkedQueue<E>`** | Lock-free Michael-Scott Queue | High-speed concurrent FIFO queue without blocking | `Queue<Message> msgQueue = new ConcurrentLinkedQueue<>();` |

---

## 5. Modern Immutable Collections

Created using factory methods (`List.of()`, `Set.of()`, `Map.of()`).

```java
// Immutable List
List<String> immutableList = List.of("Java", "Kotlin", "Scala");

// Immutable Set (rejects duplicates at construction)
Set<Integer> immutableSet = Set.of(1, 2, 3);

// Immutable Map
Map<String, Integer> immutableMap = Map.of("HTTP", 80, "HTTPS", 443);
```

### Characteristics

- **Zero modification**: Calling mutating methods (`add`, `remove`, `put`) throws `UnsupportedOperationException`.
- **Null-hostile**: Passing `null` immediately throws `NullPointerException`.
- **Space efficient**: Significantly more compact memory footprint than standard wrapper implementations.
- **Thread-safe**: Safe to share freely across virtual threads and platform threads.
