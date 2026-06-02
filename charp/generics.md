## 1. Core Generic Type Definitions

Generics allow code reuse across different data types while completely preserving type safety, memory efficiency, and compile-time checking.

| Generic Concept        | Definition / Mechanic                                                            | Primary Use Case                                                                | Example                                                              |
| :--------------------- | :------------------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :------------------------------------------------------------------- |
| **Generic Classes**    | Classes declared with one or more type parameters `[T]` acting as placeholders.  | Implementing custom data structures, wrappers, or universal business managers.  | `public class Payload<T> { public T Data { get; set; } }`            |
| **Generic Methods**    | Methods declared with type parameters independent of their containing class.     | Utility functions like parsing, mapping, swapping, or serialization.            | `public void LogData<T>(T message) { Console.WriteLine(message); }`  |
| **Generic Interfaces** | Interfaces that expose type-parameterized signatures to be implemented by types. | Decoupling architectures via standard contracts (e.g., Repositories, Handlers). | `public interface IRepository<T> { Task<T> GetByIdAsync(Guid id); }` |
| **Generic Structs**    | Value-type layouts utilizing type parameters to avoid object boxing overheads.   | High-performance components like math matrices, coordinates, or spatial points. | `public struct Frame<T> { public T Content; }`                       |

---

## 2. Generic Constraints (`where` Clauses)

Constraints instruct the compiler about what capabilities a type argument must satisfy. This avoids unsafe runtime type-casting and unlocks access to specific APIs on the type parameter.

| Constraint Syntax          | Mechanic                                                               | Primary Use Case                                                                | Example                                                      |
| :------------------------- | :--------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :----------------------------------------------------------- |
| **`where T : struct`**     | Type must be a non-nullable value type.                                | Memory-dense math blocks, high-performance binary buffers, or value wrappers.   | `public struct BitMask<T> where T : struct { }`              |
| **`where T : class`**      | Type must be a reference type (can be nullable or non-nullable).       | Identity-centric domain models, web controllers, or service managers.           | `public class CacheStore<T> where T : class { }`             |
| **`where T : notnull`**    | Type cannot be a nullable type (supports value or reference types).    | Mandatory non-null keys in dictionary indices or lookup hash tables.            | `public void IndexItem<T>(T key) where T : notnull { }`      |
| **`where T : new()`**      | Type must feature a public, parameterless constructor.                 | Factory structures or micro-dependency containers initializing blank entities.  | `public T Factory<T>() where T : new() => new T();`          |
| **`where T : BaseClass`**  | Type must inherit from or exactly match the designated base class.     | Standard OOP hierarchies where you limit generic operations to a family.        | `public class Fleet<T> where T : Vehicle { }`                |
| **`where T : IInterface`** | Type must implement the specified interface contract.                  | Enforcing explicit features like comparability, string formatting, or disposal. | `public void Run<T>(T engine) where T : IDisposable { }`     |
| **`where T : unmanaged`**  | Type must be an unmanaged value type (contains no managed references). | Low-level pointer manipulation, pinning memory, or platform interop.            | `public unsafe void Read<T>(T* ptr) where T : unmanaged { }` |

---

## 3. Generic Variance Operators (`in` and `out`)

Variance governs type safety boundaries when handling assignments among derived classes within generic interfaces and delegates.

| Operator    | Variance Type     | Behavior                                                                                                                       | Primary Use Case                                                     | Example                                                    |
| :---------- | :---------------- | :----------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------- | :--------------------------------------------------------- |
| **`out T`** | **Covariant**     | Permits using a more derived type than originally expected. The type variable can only appear as an **output** (return value). | Read-only streams, lookup interfaces, or factories pushing data out. | `public interface IProducer<out T> { T Output(); }`        |
| **`in T`**  | **Contravariant** | Permits using a less derived (more generic) type than expected. The type variable can only appear as an **input** (parameter). | Event sinks, consumers, log targets, or comparers receiving data.    | `public interface IReceiver<in T> { void Input(T data); }` |

---

## 4. Modern Generic Features (.NET 8 to C# 14 / .NET 10)

Advanced generic mechanics introduced to bypass structural limitations and optimize high-performance runtime patterns.

| Feature                      | Mechanic / Interface   | Primary Use Case                                                                      | Example                                                                  |
| :--------------------------- | :--------------------- | :------------------------------------------------------------------------------------ | :----------------------------------------------------------------------- |
| **Static Abstract Generics** | `where T : INumber<T>` | Enables math operators (`+`, `-`, `*`) and static methods to be run generically.      | `public T Add<T>(T left, T right) where T : INumber<T> => left + right;` |
| **Generic Attributes**       | `[CustomAttribute<T>]` | Replaces passing `typeof(T)` arguments inside metadata declarations with clean types. | `[ServiceValidator<OrderProcessor>] public class OrderController { }`    |
