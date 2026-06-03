## 1. Core Generic Type Definitions (Python 3.12+)

Generics in Python allow code reuse across different data types while preserving static type checking in IDEs and linters (like mypy or pyright). Python 3.12+ introduced PEP 695 syntax, providing a cleaner way to declare generic classes, functions, and type aliases using square brackets.

| Generic Concept        | Definition / Mechanic                                                            | Primary Use Case                                                                | Example                                                              |
| :--------------------- | :------------------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :------------------------------------------------------------------- |
| **Generic Classes**    | Classes declared with one or more type parameters in square brackets `[T]`.       | Implementing custom data structures, wrappers, or universal business managers.  | `class Payload[T]:\n    def __init__(self, data: T): self.data = data` |
| **Generic Functions**  | Functions declared with type parameters independent of their containing class.   | Utility functions like parsing, mapping, swapping, or serialization.            | `def log_data[T](message: T) -> None:\n    print(message)`            |
| **Generic Protocols**  | Structural interfaces that enforce generic contracts.                            | Decoupling architectures via standard contracts (e.g., Repositories, Handlers). | `class Repository[T](Protocol):\n    async def get_by_id(self, id: str) -> T: ...`|
| **Generic Type Aliases**| Reusable type shortcuts parameterized by one or more type variables.             | Defining complex structures or coordinate layouts.                              | `type Coordinate[T] = tuple[T, T]`                                   |

---

## 2. Generic Constraints and Bounds

Constraints restrict the types that can be passed as type arguments. This ensures that the type argument supports specific attributes or methods.

| Constraint / Bound Syntax | Mechanic                                                               | Primary Use Case                                                                | Example                                                      |
| :------------------------- | :--------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :----------------------------------------------------------- |
| **Type Bounds (`T: Bound`)** | Type must inherit from or exactly match the designated base class.     | Limiting generic operations to a specific class hierarchy (OOP).                | `class Fleet[T: Vehicle]:`                                   |
| **Type Constraints (`T: (A, B)`)**| Type must be one of the explicitly listed types (strict choices).    | Enforcing specific basic representations like strings or raw bytes.            | `def process[T: (str, bytes)](data: T) -> T:`                |
| **Protocol Bounds**        | Type must implement the specified structural protocol/interface methods.| Enforcing explicit behaviors like comparability, sorting, or closing.           | `def run[T: Closeable](resource: T) -> None:`                 |

---

## 3. Generic Variance (Covariance & Contravariance)

Variance governs type safety boundaries when assigning generic instances of derived classes.

*   **Covariant (`out T` equivalent)**: Allows using a more derived type than expected. The type variable can only be returned (output).
*   **Contravariant (`in T` equivalent)**: Allows using a less derived type than expected. The type variable can only be accepted (input).

In Python 3.12+, PEP 695 automatically infers the variance of generic classes and protocols based on their usage, eliminating the need to manually configure variance. For legacy Python versions, you configure variance via `TypeVar`:

```python
from typing import TypeVar

# Legacy manual declaration of covariance and contravariance
T_co = TypeVar("T_co", covariant=True)
T_contra = TypeVar("T_contra", contravariant=True)
```

---

## 4. Modern Generic Features (Python 3.12+)

Advanced generic mechanics introduced to support flexible typing and avoid complex boilerplates.

### PEP 695 Generics Syntax

No need to import `TypeVar` or explicitly declare type variables before using them in classes and functions.

```python
# Modern Generic Function
def first_item[T](items: list[T]) -> T:
    return items[0]

# Modern Generic Class
class Stack[T]:
    def __init__(self) -> None:
        self._items: list[T] = []

    def push(self, item: T) -> None:
        self._items.append(item)

    def pop(self) -> T:
        return self._items.pop()
```

### Variadic Generics (`TypeVarTuple` - Python 3.11+)

Allows writing generics that accept an arbitrary number of type arguments (useful for multi-dimensional arrays, tensors, and shape typing).

```python
from typing import TypeVarTuple, Unpack

Ts = TypeVarTuple("Ts")

class Array[Unpack[Ts]]:
    pass

# Usage: Array[int], Array[int, str], Array[int, float, str]
```

### Parameter Specifications (`ParamSpec` - Python 3.10+)

Used to capture and forward the parameter signature of one callable to another, particularly useful for writing type-safe decorators.

```python
from typing import Callable, ParamSpec, TypeVar

P = ParamSpec("P")
R = TypeVar("R")

def log_decorator(func: Callable[P, R]) -> Callable[P, R]:
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
        print("Calling function...")
        return func(*args, **kwargs)
    return wrapper
```
