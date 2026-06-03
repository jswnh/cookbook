## 1. Core Built-in Collections

These structures manage sequential and associative data in JavaScript and TypeScript runtimes. They store dynamic references to objects on the heap.

### Linear & Contiguous Collections

| Data Structure         | Underlying Structure | Primary Use Case                                                  | Time Complexity (Access / Insert)             | Example                                                       |
| :--------------------- | :------------------- | :---------------------------------------------------------------- | :-------------------------------------------- | :------------------------------------------------------------ |
| **`T[]` (Array)**      | Dynamic Array        | Resizable, sequential index-based data storage or buffer          | $O(1)$ by index / $O(1)$ amortized append     | `const weekDays: string[] = ["Mon", "Tue", "Wed"];`           |
| **`T[][]` (Jagged)**   | Array of Arrays      | Grid/matrix layouts where sub-arrays can vary in length           | $O(1)$ by index / $O(1)$ amortized append     | `const grid: number[][] = [[1], [2, 3]];`                     |
| **`[T, U]` (Tuple)**   | Fixed Array (Types)  | Fixed-size heterogeneous array treated as a single compound type  | $O(1)$ by index / N/A (Fixed size)            | `const coordinate: [number, number] = [40.71, -74.00];`      |

### Key-Value & Set Collections

| Data Structure                       | Underlying Structure   | Primary Use Case                                                         | Time Complexity (Search / Insert) | Example                                                  |
| :----------------------------------- | :--------------------- | :----------------------------------------------------------------------- | :-------------------------------- | :------------------------------------------------------- |
| **`Map<K, V>`**                      | Hash Table             | Key-value mapping supporting arbitrary keys (objects, functions, primitives) | $O(1)$ average / $O(1)$ average   | `const cache = new Map<string, User>();`                 |
| **`Set<T>`**                         | Hash Table (Keys only) | Deduplication, uniqueness checks, and quick membership checking          | $O(1)$ average / $O(1)$ average   | `const emails = new Set<string>(["a@b.com"]);`           |
| **`Record<K, V>` / `{ [key]: V }`**  | Hash Map Object        | Standard JSON/Object mapping where keys are always strings or symbols     | $O(1)$ average / $O(1)$ average   | `const config: Record<string, string> = { env: "prod" };`|

### Specialized References (Weak Collections)

| Data Structure         | Underlying Structure | Primary Use Case                                                     | Garbage Collection Behavior | Example                                              |
| :--------------------- | :------------------- | :------------------------------------------------------------------- | :-------------------------- | :--------------------------------------------------- |
| **`WeakMap<K, V>`**    | Weakly-held Hash Map | Mapping metadata to objects without preventing those objects from GC  | Keys are garbage collected  | `const meta = new WeakMap<object, string>();`        |
| **`WeakSet<T>`**       | Weakly-held Hash Set | Tracking object references uniquely without preventing GC             | Elements are GC-eligible    | `const visited = new WeakSet<object>();`             |

---

## 2. Low-Allocation & Buffer-Optimized Structures (TypedArrays)

Designed for operating directly over raw binary buffer views. These structures prevent object allocation overhead and GC pressure when dealing with graphics, WebGL, files, or network protocols.

| Structure               | Element Nature                  | Primary Use Case                                                                                            | Example                                                         |
| :---------------------- | :------------------------------ | :---------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------- |
| **`Int32Array`**        | 32-bit Signed Ints              | Fast math, index lists, high-performance numeric processing                  | `const ints = new Int32Array(256);`                             |
| **`Uint8Array`**        | 8-bit Unsigned Ints             | Byte streams, network packages, file reading buffers                       | `const bytes = new Uint8Array([0x00, 0xFF]);`                   |
| **`Float64Array`**      | 64-bit Floating Points          | Precision science computing, coordinate buffers                             | `const floats = new Float64Array(100);`                         |
| **`ArrayBuffer`**       | Contiguous raw memory           | Allocating raw memory blocks to be parsed by one or more TypedArray views   | `const buffer = new ArrayBuffer(1024);`                         |
| **`DataView`**          | Flexible buffer reader          | Reading/writing numbers at arbitrary byte offsets with explicit endianness  | `const view = new DataView(buffer);`                            |

---

## 3. High-Performance & Concurrent Shared Collections

JavaScript is fundamentally single-threaded (per context). For cross-context concurrency (Web Workers in browser, Worker Threads in Node.js), shared memory buffers are utilized.

| Structure Group / Type | Underlying Architecture     | Primary Use Case                                                                      | Example                                                                 |
| :--------------------- | :-------------------------- | :------------------------------------------------------------------------------------ | :---------------------------------------------------------------------- |
| **`SharedArrayBuffer`**| Shared raw memory buffer    | Allocating raw memory shared directly across multiple concurrent worker threads       | `const sharedBuffer = new SharedArrayBuffer(1024);`                     |
| **`Atomics` object**   | Atomic memory operations    | Providing atomic load, store, add, sub, and wait operations over shared memory buffers| `Atomics.store(new Int32Array(sharedBuffer), 0, 123);`                  |

---

## 4. Modern Read-Only and Type-Based Utilities

TypeScript supports compile-time immutability structures to enforce architectural boundaries.

| Keyword / Type Utility      | Nature                  | Primary Use Case                                                                                       | Example                                                       |
| :-------------------------- | :---------------------- | :----------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| **`ReadonlyArray<T>`**      | Immutability View       | Compile-time error when attempts are made to modify array elements or length                           | `const array: ReadonlyArray<string> = ["a", "b"];`            |
| **`ReadonlyMap<K, V>`**     | Immutability View       | Exposes Map interface without `set`, `delete`, or `clear` methods                                      | `const map: ReadonlyMap<string, number> = new Map();`         |
| **`ReadonlySet<T>`**        | Immutability View       | Exposes Set interface without `add`, `delete`, or `clear` methods                                      | `const set: ReadonlySet<string> = new Set();`                 |
| **`readonly` modifier**     | Class/Interface Field   | Restricts field modification after initial construction or assignment                                  | `interface Point { readonly x: number; readonly y: number; }` |
