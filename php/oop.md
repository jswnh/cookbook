# Object-Oriented Programming (OOP) in PHP

## 1. The Four Pillars of OOP

Object-Oriented Programming (OOP) uses classes and objects to model real-world concepts. Modern PHP features a robust class model closely aligned with C# and Java.

| Pillar            | Definition                                                                                | Purpose                                                     |
| ----------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| **Encapsulation** | Bundling data and behavior together while restricting direct access to internal state.    | Protects object integrity and reduces coupling.             |
| **Inheritance**   | Creating new classes that derive from existing classes to reuse and extend behavior.      | Promotes code reuse and establishes an "is-a" relationship. |
| **Polymorphism**  | Allowing different implementations to be accessed through a common interface or base type.| Enables flexible and extensible designs.                    |
| **Abstraction**   | Exposing only essential behavior while hiding implementation details.                     | Reduces complexity and improves maintainability.            |

### Example

```php
// ABSTRACTION
interface NotificationChannel
{
    public function send(string $message): void;
}

// ENCAPSULATION + INHERITANCE
abstract class NotificationBase implements NotificationChannel
{
    public readonly string $senderAddress;
    private string $status = "Pending";

    public function __construct(string $senderAddress)
    {
        $this->senderAddress = $senderAddress;
    }

    public function getStatus(): string
    {
        return $this->status;
    }

    protected function updateStatus(string $newStatus): void
    {
        $this->status = $newStatus;
    }

    abstract public function send(string $message): void;
}

// INHERITANCE + POLYMORPHISM
class EmailNotification extends NotificationBase
{
    public readonly string $recipientEmail;

    public function __construct(string $senderAddress, string $recipientEmail)
    {
        parent::__construct($senderAddress);
        $this->recipientEmail = $recipientEmail;
    }

    public function send(string $message): void
    {
        echo "[Email] From: {$this->senderAddress} To: {$this->recipientEmail}\n";
        echo "Content: {$message}\n";

        $this->updateStatus("Dispatched via SMTP");
    }
}

class SmsNotification extends NotificationBase
{
    public readonly string $mobileNumber;

    public function __construct(string $senderAddress, string $mobileNumber)
    {
        parent::__construct($senderAddress);
        $this->mobileNumber = $mobileNumber;
    }

    public function send(string $message): void
    {
        echo "[SMS] To: {$this->mobileNumber}\n";
        echo "Content: {$message}\n";

        $this->updateStatus("Sent via Telecom Gateway");
    }
}
```

---

## 2. Access Modifiers

Access modifiers define where class properties and methods can be accessed.

| Modifier             | Accessibility                                                             |
| -------------------- | ------------------------------------------------------------------------- |
| `public`             | Accessible from anywhere (default if omitted).                            |
| `private`            | Accessible only within the defining class.                                |
| `protected`          | Accessible within the defining class and its subclasses.                  |

### Example

```php
class AccountManager
{
    public string $publicIdentifier = "PUB-1029";
    private float $accountBalance = 5500.25;
    protected string $internalNotes = "Review required";

    public function processTransaction(float $amount): void
    {
        $this->accountBalance += $amount;
    }
}

class PremiumAccountManager extends AccountManager
{
    public function displayNotes(): void
    {
        echo $this->internalNotes . "\n"; // Allowed

        // Compilation / Runtime error:
        // echo $this->accountBalance;
    }
}
```

---

## 3. Abstract Classes vs Interfaces

| Feature          | Purpose                                                                   | Can Contain State? |
| ---------------- | ------------------------------------------------------------------------- | ------------------ |
| **Virtual Methods**| All methods in PHP are virtual (overridable) by default unless marked `final`.| Yes                |
| **`abstract class`**| Provides shared state and behavior while forcing subclasses to override abstract methods.| Yes                |
| **`interface`**  | Defines a contract that classes must implement.                           | No state           |

### Example

```php
interface Renderable
{
    public function render(): void;
}

abstract class UIComponent implements Renderable
{
    public function __construct(
        public int $width,
        public int $height
    ) {}

    abstract public function render(): void;

    public function handleInput(): void
    {
        echo "Standard UI input processed.\n";
    }
}

class Button extends UIComponent
{
    public string $label = "Submit";

    public function render(): void
    {
        echo "Rendering button [{$this->label}] with dimensions {$this->width}x{$this->height}\n";
    }

    public function handleInput(): void
    {
        parent::handleInput();
        echo "Button click event triggered.\n";
    }
}
```

---

## 4. Modern PHP OOP Features

Modern PHP provides features that reduce boilerplate and improve safety.

| Feature                    | Description                                                       | Use Case                                                      |
| -------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------- |
| **Constructor Promotion**  | Declares and initializes properties directly in constructor.      | Eliminating boilerplate constructor code (PHP 8.0+).          |
| **Readonly Properties**    | Properties that can only be written once during construction.     | Immutable data models (PHP 8.1+).                             |
| **Readonly Classes**       | All properties of the class are implicitly readonly.              | Immutable DTOs and Value Objects (PHP 8.2+).                 |
| **`final` keyword**        | Prevents inheritance on classes or overriding on methods.         | Protecting closed implementation designs (analogous to `sealed`). |
| **Enums**                  | Native enumerations supporting methods and values (Backed Enums).  | Strongly-typed set of choices (PHP 8.1+).                     |

### Constructor Promotion & Readonly Class Example

```php
readonly class Order
{
    public function __construct(
        public string $orderId,
        public float $totalCost
    ) {}
}

$order = new Order("ORD-123", 99.99);
// $order->totalCost = 150.00; // Error: Cannot modify readonly property
```

### Backed Enum Example

```php
enum Status: string
{
    case Pending = 'pending';
    case Approved = 'approved';
    case Denied = 'denied';

    public function getLabel(): string
    {
        return match($this) {
            self::Pending => 'Waiting Approval',
            self::Approved => 'Order Approved',
            self::Denied => 'Order Denied',
        };
    }
}
```

---

# Summary

OOP in PHP is highly aligned with modern type-safe standards:

- **Encapsulation** is controlled using `public`, `private`, and `protected`.
- **Inheritance** uses class hierarchies (`extends`).
- **Polymorphism** is achieved through abstract class overrides and interface contracts.
- **Abstraction** uses interfaces and abstract base classes.

Modern PHP enhances OOP with **Constructor Property Promotion**, **Readonly Classes**, **final constraints**, and native **Backed Enums** to write robust, testable, and self-documenting applications.
