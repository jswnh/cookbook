# Object-Oriented Programming (OOP) in C#

## 1. The Four Pillars of OOP

Object-Oriented Programming (OOP) uses objects to model real-world entities and behaviors. These four principles help create maintainable, reusable, and scalable software.

| Pillar            | Definition                                                                                | Purpose                                                     |
| ----------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| **Encapsulation** | Bundling data and behavior together while restricting direct access to internal state.    | Protects object integrity and reduces coupling.             |
| **Inheritance**   | Creating new classes that derive from existing classes to reuse and extend behavior.      | Promotes code reuse and establishes an "is-a" relationship. |
| **Polymorphism**  | Allowing different implementations to be accessed through a common contract or base type. | Enables flexible and extensible designs.                    |
| **Abstraction**   | Exposing only essential behavior while hiding implementation details.                     | Reduces complexity and improves maintainability.            |

### Example

```csharp
using System;

// ABSTRACTION
public interface INotificationChannel
{
    void Send(string message);
}

// ENCAPSULATION + INHERITANCE
public abstract class NotificationBase : INotificationChannel
{
    public required string SenderAddress { get; init; }

    private string _status = "Pending";

    public string GetStatus() => _status;

    protected void UpdateStatus(string newStatus)
    {
        _status = newStatus;
    }

    public abstract void Send(string message);
}

// INHERITANCE + POLYMORPHISM
public class EmailNotification : NotificationBase
{
    public required string RecipientEmail { get; init; }

    public override void Send(string message)
    {
        Console.WriteLine($"[Email] From: {SenderAddress} To: {RecipientEmail}");
        Console.WriteLine($"Content: {message}");

        UpdateStatus("Dispatched via SMTP");
    }
}

public class SmsNotification : NotificationBase
{
    public required string MobileNumber { get; init; }

    public override void Send(string message)
    {
        Console.WriteLine($"[SMS] To: {MobileNumber}");
        Console.WriteLine($"Content: {message}");

        UpdateStatus("Sent via Telecom Gateway");
    }
}
```

---

# 2. Access Modifiers

Access modifiers define where types and members can be accessed.

| Modifier             | Accessibility                                                             |
| -------------------- | ------------------------------------------------------------------------- |
| `public`             | Accessible from anywhere.                                                 |
| `private`            | Accessible only within the containing type.                               |
| `protected`          | Accessible within the containing type and derived classes.                |
| `internal`           | Accessible only within the same assembly.                                 |
| `protected internal` | Accessible from the same assembly OR derived classes in other assemblies. |
| `private protected`  | Accessible only within the same assembly and derived classes.             |

### Example

```csharp
public class AccountManager
{
    public string PublicIdentifier = "PUB-1029";

    private decimal _accountBalance = 5500.25m;

    protected string InternalNotes = "Review required";

    internal string SystemCode = "SYS_ALPHA_9";

    public void ProcessTransaction(decimal amount)
    {
        _accountBalance += amount;
    }
}

public class PremiumAccountManager : AccountManager
{
    public void DisplayNotes()
    {
        Console.WriteLine(InternalNotes);

        // Compilation error:
        // Console.WriteLine(_accountBalance);
    }
}
```

---

# 3. Virtual vs Abstract vs Interface

| Feature          | Purpose                                                                   | Can Contain State? |
| ---------------- | ------------------------------------------------------------------------- | ------------------ |
| `virtual`        | Provides a default implementation that can be overridden.                 | Yes                |
| `abstract class` | Provides shared state and behavior while forcing certain implementations. | Yes                |
| `interface`      | Defines a contract that implementing types must satisfy.                  | No instance state  |

> Modern C# interfaces may contain default method implementations, but they cannot contain instance fields or instance state.

### Example

```csharp
public interface IRenderable
{
    void Render();
}

public abstract class UIComponent : IRenderable
{
    public int Width { get; set; }
    public int Height { get; set; }

    public abstract void Render();

    public virtual void HandleInput()
    {
        Console.WriteLine("Standard UI input processed.");
    }
}

public class Button : UIComponent
{
    public string Label { get; set; } = "Submit";

    public override void Render()
    {
        Console.WriteLine(
            $"Rendering button [{Label}] with dimensions {Width}x{Height}"
        );
    }

    public override void HandleInput()
    {
        base.HandleInput();
        Console.WriteLine("Button click event triggered.");
    }
}
```

---

# 4. Modern C# Features

Modern C# provides features that reduce boilerplate and improve correctness.

| Feature                    | Description                                                       | Use Case                                                      |
| -------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------- |
| Primary Constructors       | Constructor parameters declared directly on the type declaration. | Dependency injection and simplified initialization.           |
| `record` / `record struct` | Types with value-based equality and concise syntax.               | DTOs and immutable data models.                               |
| `required`                 | Forces callers to initialize a member during object creation.     | Preventing incomplete object construction.                    |
| `init`                     | Allows setting a property only during initialization.             | Immutable objects.                                            |
| `sealed`                   | Prevents inheritance or overriding.                               | Protecting closed implementations and enabling optimizations. |

## Primary Constructor Example

```csharp
public interface IDatabaseConnection
{
    void Save(Order order);
}

public interface ILogger
{
    void Log(string message);
}

public class OrderProcessor(
    IDatabaseConnection connection,
    ILogger logger)
{
    public void ExecuteOrder(Order order)
    {
        logger.Log($"Processing order {order.OrderId}");
        connection.Save(order);
    }
}
```

## Record Example

```csharp
public record Order
{
    public required Guid OrderId { get; init; }

    public required decimal TotalCost { get; init; }
}
```

### Value-Based Equality

```csharp
var order1 = new Order
{
    OrderId = Guid.Parse("11111111-1111-1111-1111-111111111111"),
    TotalCost = 100
};

var order2 = new Order
{
    OrderId = Guid.Parse("11111111-1111-1111-1111-111111111111"),
    TotalCost = 100
};

Console.WriteLine(order1 == order2); // True
```

## Sealed Class Example

```csharp
public sealed class HighlySecureProcessor
{
    public void Execute()
    {
        Console.WriteLine("Secure processing...");
    }
}

// Compilation error:
// public class CustomProcessor : HighlySecureProcessor
// {
// }
```

---

# Summary

OOP in C# revolves around four core principles:

- **Encapsulation** protects state.
- **Inheritance** enables reuse.
- **Polymorphism** allows interchangeable behavior.
- **Abstraction** hides complexity behind contracts.

Modern C# enhances OOP with:

- Primary constructors
- Records
- Required members
- Init-only properties
- Sealed hierarchies

These features help build safer, cleaner, and more maintainable applications.
