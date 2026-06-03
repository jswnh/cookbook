## 1. Mutable vs Immutable Types

Python divides types into mutable (modifiable in place) and immutable (cannot be changed after creation, modifications create new objects).

### Immutable Types (Value-Like Types)

These types are immutable and hashable (can be keys in dictionaries or elements of sets).

| Keyword / Type  | C# Equivalent    | Description                                             | Example                                    |
| :-------------- | :--------------- | :------------------------------------------------------ | :----------------------------------------- |
| **`int`**       | `int`, `long`    | Arbitrary-precision integer (automatically resizes)      | `population = 1500000`                     |
| **`float`**     | `double`         | Double-precision floating point (64-bit C double)        | `pi = 3.1415926535`                        |
| **`complex`**   | `Complex`        | Complex number with real and imaginary parts            | `num = 3 + 4j`                             |
| **`bool`**      | `bool`           | Logical values (`True` or `False`)                      | `is_active = True`                         |
| **`str`**       | `string`         | Immutable sequence of Unicode characters                | `message = "Hello, World!"`                |
| **`tuple`**     | `ValueTuple`     | Immutable sequence of heterogeneous objects             | `point = (10, 20)`                         |
| **`bytes`**     | `byte[]`         | Immutable sequence of bytes                             | `data = b"\x00\x01\x02"`                   |
| **`frozenset`** | `SortedSet`      | Immutable version of a set                              | `frozen_emails = frozenset(["a@b.com"])`   |

### Mutable Types (Reference-Like Types)

These types can be mutated in place and are generally not hashable.

| Keyword / Type  | C# Equivalent    | Description                                             | Example                                    |
| :-------------- | :--------------- | :------------------------------------------------------ | :----------------------------------------- |
| **`list`**      | `List<T>`        | Mutable ordered sequence of objects                     | `items = [1, 2, 3]`                        |
| **`dict`**      | `Dictionary<K,V>`| Mutable mapping of keys to values                       | `cache = {"id": 1}`                        |
| **`set`**       | `HashSet<T>`     | Mutable unordered collection of unique values           | `skills = {"Python", "SQL"}`               |
| **`bytearray`** | `byte[]`         | Mutable version of `bytes`                              | `buffer = bytearray(5)`                    |

---

## 2. High-Precision & Rational Numeric Types

Specialized types for high-precision math or exact fractions.

| Type            | Module           | Best For                                             | Example                                    |
| :-------------- | :--------------- | :--------------------------------------------------- | :----------------------------------------- |
| **`Decimal`**   | `decimal`        | Financial calculations (arbitrary precision decimal) | `balance = Decimal("1999.99")`             |
| **`Fraction`**  | `fractions`      | Rational numbers (numerator / denominator)           | `half = Fraction(1, 2)`                    |

---

## 3. Reference and Structural Typing

Python is dynamically typed but supports static type hints (via the `typing` module) for IDE/linter validation.

| Type Hint       | C# Analog         | Description                                               | Example                                                  |
| :-------------- | :---------------- | :-------------------------------------------------------- | :------------------------------------------------------- |
| **`Any`**       | `dynamic` / `object`| Bypasses type checking                                    | `data: Any = fetch_data()`                               |
| **`object`**    | `object`          | The base class of all types in Python                     | `obj: object = None`                                     |
| **`Callable`**  | `delegate` / `Func`| Represents a reference to a function or method            | `handler: Callable[[str], None] = print`                 |
| **`Protocol`**  | `interface`       | Defines structural subtyping / duck typing contracts      | `class Repository(Protocol):\n    def save(self): ...`    |
| **`Union` / `\|`**| (Generic constraints)| Allows multiple types (e.g., `str \| int` in Python 3.10+) | `value: str \| int = 10`                                 |
| **`Optional[T]`**| `T?`             | Represents value of type `T` or `None`                    | `name: str \| None = None`                               |

---

## 4. Memory Pointers and C-Types (Advanced)

Allows accessing raw C-level pointers and values using the built-in `ctypes` module.

| Type              | Description                                       | Example                                     |
| :---------------- | :------------------------------------------------ | :------------------------------------------ |
| **`ctypes.pointer`**| Obtains a pointer to an object / raw memory address | `import ctypes\nx = ctypes.c_int(10)\nptr = ctypes.pointer(x)` |
