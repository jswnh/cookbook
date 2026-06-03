# Object-Oriented Programming (OOP) in Python

## 1. The Four Pillars of OOP

Object-Oriented Programming (OOP) uses classes and objects to model real-world entities and behaviors. Python is a dynamically-typed, multi-paradigm language where OOP is fully integrated.

| Pillar            | Definition                                                                                | Purpose                                                     |
| ----------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| **Encapsulation** | Bundling data and behavior together while restricting direct access to internal state.    | Protects object integrity and reduces coupling.             |
| **Inheritance**   | Creating new classes that derive from existing classes to reuse and extend behavior.      | Promotes code reuse and establishes an "is-a" relationship. |
| **Polymorphism**  | Allowing different implementations to be accessed through a common interface or base type.| Enables flexible and extensible designs.                    |
| **Abstraction**   | Exposing only essential behavior while hiding implementation details.                     | Reduces complexity and improves maintainability.            |

### Example

```python
from abc import ABC, abstractmethod

# ABSTRACTION (using Protocol or ABC)
class NotificationChannel(ABC):
    @abstractmethod
    def send(self, message: str) -> None:
        pass

# ENCAPSULATION + INHERITANCE
class NotificationBase(NotificationChannel, ABC):
    def __init__(self, sender_address: str):
        self.sender_address = sender_address  # public
        self._status = "Pending"              # protected (by convention)

    def get_status(self) -> str:
        return self._status

    def _update_status(self, new_status: str) -> None:
        self._status = new_status

# INHERITANCE + POLYMORPHISM
class EmailNotification(NotificationBase):
    def __init__(self, sender_address: str, recipient_email: str):
        super().__init__(sender_address)
        self.recipient_email = recipient_email

    def send(self, message: str) -> None:
        print(f"[Email] From: {self.sender_address} To: {self.recipient_email}")
        print(f"Content: {message}")
        self._update_status("Dispatched via SMTP")

class SmsNotification(NotificationBase):
    def __init__(self, sender_address: str, mobile_number: str):
        super().__init__(sender_address)
        self.mobile_number = mobile_number

    def send(self, message: str) -> None:
        print(f"[SMS] To: {self.mobile_number}")
        print(f"Content: {message}")
        self._update_status("Sent via Telecom Gateway")
```

---

## 2. Access Modifiers (Access Control)

Python does not enforce access control at the compiler level. Instead, it uses conventions and name mangling to indicate member visibility.

| Identifier Style    | Python Convention                                                         |
| ------------------- | ------------------------------------------------------------------------- |
| `name`              | **Public**: Accessible from anywhere.                                      |
| `_name`             | **Protected**: Internal to the class and subclasses (convention only).     |
| `__name`            | **Private**: Triggers *name mangling* (renamed internally to `_Class__name` to prevent accidental overriding). |

### Example

```python
class AccountManager:
    def __init__(self) -> None:
        self.public_identifier = "PUB-1029"
        self.__account_balance = 5500.25           # Private (Mangled)
        self._internal_notes = "Review required"   # Protected
        self.system_code = "SYS_ALPHA_9"

    def process_transaction(self, amount: float) -> None:
        self.__account_balance += amount

class PremiumAccountManager(AccountManager):
    def display_notes(self) -> None:
        print(self._internal_notes)  # Allowed (protected)
        
        # print(self.__account_balance)  # AttributeError! (Private to parent class)
```

---

## 3. Abstract Classes vs Protocols (Interfaces)

Python provides two primary mechanisms to define interfaces and abstract behaviors:

| Feature          | Purpose                                                                   | Can Contain Instance State? |
| ---------------- | ------------------------------------------------------------------------- | ------------------ |
| **`virtual` (Default)**| In Python, all methods are virtual by default and can be overridden. | Yes |
| **Abstract Class (`abc.ABC`)**| Provides shared state and behavior while forcing subclasses to override abstract methods (nominal inheritance).| Yes |
| **Protocol (`typing.Protocol`)**| Defines a structural interface (duck typing / static typing) without requiring inheritance. | No instance state |

### Example

```python
from abc import ABC, abstractmethod
from typing import Protocol

# Structural Interface using Protocol
class Renderable(Protocol):
    def render(self) -> None: ...

# Abstract Class
class UIComponent(ABC):
    def __init__(self, width: int, height: int) -> None:
        self.width = width
        self.height = height

    @abstractmethod
    def render(self) -> None:
        pass

    def handle_input(self) -> None:
        print("Standard UI input processed.")

# concrete class
class Button(UIComponent):
    def __init__(self, width: int, height: int, label: str = "Submit") -> None:
        super().__init__(width, height)
        self.label = label

    def render(self) -> None:
        print(f"Rendering button [{self.label}] with dimensions {self.width}x{self.height}")

    def handle_input(self) -> None:
        super().handle_input()
        print("Button click event triggered.")
```

---

## 4. Modern Python OOP Features

Python includes tools to reduce boilerplate, implement immutable states, and protect structures from inheritance.

| Feature                    | Description                                                       | Use Case                                                      |
| -------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------- |
| **`@dataclass`**           | Automatically generates constructor, representation, and equality methods.| DTOs and clean data representations.                          |
| **`frozen=True`**          | Dataclass setting that enforces immutability.                     | Creating read-only / immutable data models.                   |
| **`@final` decorator**     | Declares that a class cannot be subclassed or a method overridden. | Protecting closed implementations (analogous to `sealed`).    |
| **`Final` type hint**      | Enforces that a variable or attribute cannot be reassigned.       | Read-only constants.                                          |

### Dataclass Example

```python
from dataclasses import dataclass

@dataclass
class Order:
    order_id: str
    total_cost: float
```

### Value-Based Equality

```python
order1 = Order("11111111-1111", 100.0)
order2 = Order("11111111-1111", 100.0)

print(order1 == order2)  # True
```

### Sealed Class / Final Method Example

```python
from typing import final

@final
class HighlySecureProcessor:
    def execute(self) -> None:
        print("Secure processing...")

# Static analysis tools (like mypy) will throw an error:
# class CustomProcessor(HighlySecureProcessor):
#     pass
```

---

# Summary

OOP in Python is expressive and relies on conventions rather than strict compiler constraints:

- **Encapsulation** protects state using naming conventions (`_` and `__`).
- **Inheritance** supports multiple inheritance natively.
- **Polymorphism** is natural through dynamic duck typing.
- **Abstraction** uses abstract base classes (`abc.ABC`) and structural typing (`typing.Protocol`).

Modern Python leverages `@dataclass` (with `frozen=True`) and `@final` to provide safety, structure, and readability to OOP architectures.
