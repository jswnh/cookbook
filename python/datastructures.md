## 1. Core Built-in Collections

These structures manage sequential and associative data in Python. Most built-in structures are dynamic and store references to objects.

### Linear & Contiguous Collections

| Data Structure         | Underlying Structure | Primary Use Case                                                  | Time Complexity (Access / Insert)             | Example                                                       |
| :--------------------- | :------------------- | :---------------------------------------------------------------- | :-------------------------------------------- | :------------------------------------------------------------ |
| **`list`**             | Dynamic Array        | Default choice for resizable, sequential index-based data         | $O(1)$ by index / $O(1)$ amortized append     | `week_days = ["Mon", "Tue", "Wed"]`                           |
| **`tuple`**            | Fixed-size Array     | Immutable sequence of items; hashable, safe for keys/sets         | $O(1)$ by index / N/A (Fixed size)            | `point = (10, 20)`                                            |
| **`collections.deque`**| Doubly-Linked List   | Fast appends and pops from both ends (Queues, Stacks, Pipelines)  | $O(n)$ access / $O(1)$ insert at either end   | `history = deque(maxlen=10)`                                  |
| **`array.array`**      | Contiguous Raw Array | Homogeneous basic values (integers, floats) with low memory footprint| $O(1)$ by index / $O(1)$ amortized append     | `buffer = array('i', [1, 2, 3])`                              |

### Key-Value & Set Collections

| Data Structure         | Underlying Structure   | Primary Use Case                                                         | Time Complexity (Search / Insert) | Example                                                  |
| :--------------------- | :--------------------- | :----------------------------------------------------------------------- | :-------------------------------- | :------------------------------------------------------- |
| **`dict`**             | Hash Table             | High-speed lookups mapping unique keys to values (Insertion-ordered)     | $O(1)$ average / $O(1)$ average   | `user_cache = {"id1": user_obj}`                         |
| **`set`**              | Hash Table (Keys only) | High-performance deduplication, uniqueness checks, and mathematical sets | $O(1)$ average / $O(1)$ average   | `emails = {"a@b.com", "b@c.com"}`                        |
| **`frozenset`**        | Hash Table (Keys only) | Immutable, hashable version of set (safe for dictionary keys/other sets) | $O(1)$ average / N/A (Immutable)  | `frozen_emails = frozenset(["a@b.com"])`                 |
| **`defaultdict`**      | Hash Table             | Dictionary subclass that calls a factory function for missing keys       | $O(1)$ average / $O(1)$ average   | `groups = defaultdict(list)`                             |

### Specialized Collections

| Data Structure         | Underlying Structure | Primary Use Case                                                     | Operations               | Example                                              |
| :--------------------- | :------------------- | :------------------------------------------------------------------- | :----------------------- | :--------------------------------------------------- |
| **`Counter`**          | Hash Table (`dict`)  | Counting hashable objects                                            | `most_common()`, math ops| `counts = Counter("abracadabra")`                    |
| **`queue.Queue`**      | Deque with Locks     | Multi-producer, multi-consumer FIFO queue for safe thread access     | `put()`, `get()`         | `task_queue = Queue()`                               |
| **`queue.PriorityQueue`**| Binary Heap (heapq) | Thread-safe queue where elements are retrieved in priority order     | `put()`, `get()`         | `pq = PriorityQueue()`                               |
| **`OrderedDict`**      | Dict + Linked List   | Dictionary subclass that remembers its order (useful for LRU caches)| `popitem(last=False)`    | `od = OrderedDict()`                                 |

---

## 2. Low-Allocation & Memory-Optimized Structures

Introduced to minimize memory footprint and optimize raw data parsing by operating directly over byte structures or restricting class-instance dynamic dictionary overheads.

| Structure               | Memory Nature                   | Primary Use Case                                                                                            | Example                                                         |
| :---------------------- | :------------------------------ | :---------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------- |
| **`memoryview`**        | Shared Memory Buffer            | Zero-copy slicing and buffer access of binary data without copying                                           | `view = memoryview(bytearray(b"data"))`                          |
| **`__slots__`**         | Static Instance Struct          | Replaces `__dict__` in classes to save memory and increase access speed for instance variables               | `class Point: __slots__ = ("x", "y")`                           |
| **`struct` module**     | Binary Packed C-Structs         | Converting between Python values and C structs represented as Python bytes (e.g. file headers, network payloads)| `binary_data = struct.pack("ii", 10, 20)`                       |

---

## 3. High-Performance & Thread-Safe Collections

Designed for concurrency, multi-threaded work, or advanced structural manipulation.

| Structure Group / Type | Underlying Architecture     | Primary Use Case                                                                      | Example                                                                 |
| :--------------------- | :-------------------------- | :------------------------------------------------------------------------------------ | :---------------------------------------------------------------------- |
| **`queue.Queue`**      | Thread locking wrapper      | Thread-safe multi-producer/multi-consumer FIFO pipeline                               | `jobs = Queue(maxsize=100)`                                             |
| **`collections.deque`**| GIL-thread-safe appends/pops| Extremely fast thread-safe push/pop from ends (without full locking overhead)         | `fast_buffer = deque()`                                                 |
| **`multiprocessing.Queue`**| Pipes & Shared Locks    | Inter-process safe queue for passing objects between processes                        | `proc_queue = multiprocessing.Queue()`                                  |
| **`types.MappingProxyType`**| Read-only view on dict | Exposing read-only dictionary interfaces to prevent caller modification               | `read_only = MappingProxyType({"status": "OK"})`                        |

---

## 4. Modern Immutable and Dataclass-Based Structures

| Keyword / Type          | Nature                  | Primary Use Case                                                                                       | Example                                                       |
| :---------------------- | :---------------------- | :----------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| **`types.MappingProxyType`**| Reference Wrapper  | Read-only wrapper safe to expose publicly from modules or domains                                      | `proxy = MappingProxyType(internal_dict)`                     |
| **`dataclass(frozen=True)`**| Value-based Object   | Lightweight class featuring automatic immutability, type hints, constructor, and value equality        | `@dataclass(frozen=True)\nclass GeoPoint:\n    lat: float\n    lng: float` |
| **`NamedTuple`**        | Value-based Tuple       | Tuple subclass with named fields, value equality, and static typing support                           | `class Coordinate(NamedTuple):\n    x: int\n    y: int`   |
