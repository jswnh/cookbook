# 1. Functions / Methods

A function is a named block of code that performs an action. When inside a class, it is referred to as a method.

```python
def greet() -> None:
    print("Hello, World!")

greet()
```

Output:

```text
Hello, World!
```

---

# 2. Method Parameters

Methods can accept input values.

```python
def greet(name: str) -> None:
    print(f"Hello, {name}!")

greet("John")
```

Output:

```text
Hello, John!
```

---

# 3. Returning Values

Methods can return data.

```python
def add(a: int, b: int) -> int:
    return a + b

result = add(10, 20)

print(result)
```

Output:

```text
30
```

---

# 4. Lambda Functions (Expression-Bodied Functions)

Useful for short, one-line functions.

```python
square = lambda number: number * number

print(square(5))
```

Output:

```text
25
```

---

# 5. Optional Parameters (Default Parameter Values)

Provide default values for parameters.

```python
def greet(name: str, greeting: str = "Hello") -> None:
    print(f"{greeting}, {name}!")

greet("John")
greet("Jane", "Welcome")
```

---

# 6. Keyword Arguments (Named Arguments)

Improve readability and allow arguments to be passed in any order.

```python
def create_user(name: str, age: int, email: str) -> None:
    print(name)

create_user(name="John", age=25, email="john@example.com")
```

---

# 7. Method Overloading

Python does not natively support method overloading with the same name. However, you can write type hints for static analysis using `@typing.overload` and a single runtime implementation.

```python
from typing import overload, Union

@overload
def add(a: int, b: int) -> int: ...

@overload
def add(a: float, b: float) -> float: ...

def add(a: Union[int, float], b: Union[int, float]) -> Union[int, float]:
    return a + b
```

---

# 8. Modifying Mutable Parameters (Analogous to `ref`)

Python passes objects by assignment (similar to passing references by value). To modify a variable outside the function scope, you pass a mutable object (like a list or dict) or wrap it in a custom class.

```python
class Ref[T]:
    def __init__(self, value: T):
        self.value = value

def increment(ref_value: Ref[int]) -> None:
    ref_value.value += 1

number = Ref(10)

increment(number)

print(number.value)
```

Output:

```text
11
```

---

# 9. Returning Multiple Values (Analogous to `out`)

Python handles multiple return values natively by returning a tuple, which can then be unpacked.

```python
def get_user() -> tuple[str, str]:
    first_name = "John"
    last_name = "Doe"
    return first_name, last_name

first, last = get_user()

print(f"{first} {last}")
```

---

# 10. Arbitrary Arguments (`*args` and `**kwargs`)

Accept a variable number of positional or keyword arguments (analogous to `params`).

```python
def sum_numbers(*numbers: int) -> int:
    return sum(numbers)

print(sum_numbers(1, 2, 3, 4, 5))
```

Output:

```text
15
```

---

# 11. Local / Nested Functions

Functions inside other functions.

```python
def process() -> None:
    def add(a: int, b: int) -> int:
        return a + b

    print(add(10, 20))

process()
```

---

# 12. Instance Methods

Require a class instance and automatically receive the instance as `self`.

```python
class Calculator:
    def add(self, a: int, b: int) -> int:
        return a + b

calculator = Calculator()

print(calculator.add(10, 20))
```

---

# 13. Static Methods

Belong to the class namespace rather than an instance. They do not receive `self` or `cls`.

```python
class MathHelper:
    @staticmethod
    def double(value: int) -> int:
        return value * 2

print(MathHelper.double(10))
```

---

# 14. Class Methods

Belong to the class and receive the class object as their first parameter `cls` (useful for alternative constructors).

```python
class User:
    def __init__(self, name: str):
        self.name = name

    @classmethod
    def anonymous(cls) -> "User":
        return cls("Guest")
```

---

# 15. Properties

Properties provide controlled access to object attributes.

## Auto-Implemented Attribute

```python
class User:
    def __init__(self):
        self.name: str = ""
```

---

## Read-Only Property (Getter)

```python
class User:
    def __init__(self, name: str):
        self._name = name

    @property
    def name(self) -> str:
        return self._name
```

---

## Custom Getter and Setter

Adds validation or custom logic when getting or setting values.

```python
class Product:
    def __init__(self, price: float):
        self._price = price

    @property
    def price(self) -> float:
        return self._price

    @price.setter
    def price(self, value: float) -> None:
        if value < 0:
            raise ValueError("Price cannot be negative.")
        self._price = value
```

---

# 16. Dataclasses and Init-Only Properties

Using Python's built-in `dataclasses` module for constructor generation and immutable structures.

```python
from dataclasses import dataclass

@dataclass(frozen=True)  # Read-only / Immutable properties
class User:
    name: str
```

Usage:

```python
user = User(name="John")
# user.name = "Jane"  # Raises FrozenInstanceError
```

---

# Summary

## Functions & Methods

- Def declarations & Lambdas
- Positional, keyword, default parameters
- Variable arguments (`*args`, `**kwargs`)
- Return multiple values as tuples
- Local (nested) functions
- `@staticmethod` and `@classmethod`

## Properties

- `@property` getter decorator
- `@name.setter` setter decorator
- Dataclasses for declarative constructors
- Immutable classes with `frozen=True`

Functions are first-class citizens in Python. Methods are simply functions attached to class namespaces that receive the instance (`self`) or class (`cls`) dynamically when called.
