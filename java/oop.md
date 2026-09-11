# Object-Oriented Programming (OOP) in Java

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

## 1. The Four Pillars of OOP

Object-Oriented Programming models real-world domain concepts and processes through structured entities called objects.

| Pillar | Definition | Purpose |
| :--- | :--- | :--- |
| **Encapsulation** | Hiding internal object state and requiring all interactions to occur through public methods. | Protects data integrity, prevents unintended side effects, and decouples callers from internal implementation. |
| **Inheritance** | Deriving a new class from an existing parent class (`extends`) to share state and behavior. | Establishes an "is-a" relationship and promotes code reuse. |
| **Polymorphism** | Enabling a single interface or base class reference to execute different concrete behaviors at runtime. | Makes software extensible, interchangeable, and easily pluggable. |
| **Abstraction** | Exposing essential behavior while concealing complex implementation details behind interfaces or abstract classes. | Reduces cognitive complexity and separates contract from execution. |

---

### Example Demonstrating All Four Pillars

```java
import java.util.List;

// 1. ABSTRACTION: High-level contract
public interface PaymentGateway {
    PaymentResult process(double amount);
}

public record PaymentResult(boolean success, String transactionId, String message) {}

// 2. ENCAPSULATION + INHERITANCE: Abstract base with protected state
public abstract class BasePaymentGateway implements PaymentGateway {
    private final String gatewayName; // Encapsulated private field
    private int processedCount = 0;

    protected BasePaymentGateway(String gatewayName) {
        if (gatewayName == null || gatewayName.isBlank()) {
            throw new IllegalArgumentException("Gateway name cannot be blank");
        }
        this.gatewayName = gatewayName;
    }

    public String getGatewayName() {
        return gatewayName;
    }

    protected void recordTransaction() {
        this.processedCount++;
    }

    public int getProcessedCount() {
        return processedCount;
    }
}

// 3. INHERITANCE & POLYMORPHISM: Concrete implementations
public class CreditCardGateway extends BasePaymentGateway {
    private final String merchantId;

    public CreditCardGateway(String merchantId) {
        super("CreditCard");
        this.merchantId = merchantId;
    }

    @Override
    public PaymentResult process(double amount) {
        recordTransaction();
        String txId = "CC-" + System.currentTimeMillis();
        return new PaymentResult(true, txId, "Charged $%.2f via Merchant %s".formatted(amount, merchantId));
    }
}

public class CryptoGateway extends BasePaymentGateway {
    private final String walletAddress;

    public CryptoGateway(String walletAddress) {
        super("Crypto");
        this.walletAddress = walletAddress;
    }

    @Override
    public PaymentResult process(double amount) {
        recordTransaction();
        String txId = "0x" + Long.toHexString(System.currentTimeMillis());
        return new PaymentResult(true, txId, "Transferred $%.2f to wallet %s".formatted(amount, walletAddress));
    }
}

// POLYMORPHIC DISPATCH
public class CheckoutService {
    public static void main(String[] args) {
        List<PaymentGateway> gateways = List.of(
            new CreditCardGateway("MERCH-9941"),
            new CryptoGateway("0x71C...B29")
        );

        for (PaymentGateway gateway : gateways) {
            PaymentResult result = gateway.process(99.50);
            System.out.printf("[%s] TX: %s -> %s%n", 
                result.success() ? "SUCCESS" : "FAILED", 
                result.transactionId(), 
                result.message()
            );
        }
    }
}
```

---

# 2. Access Modifiers

Java provides four levels of visibility to regulate encapsulation boundaries:

| Modifier | Within Same Class | Within Same Package | Subclasses (Any Package) | Everywhere (Public) |
| :--- | :---: | :---: | :---: | :---: |
| **`private`** | Yes | No | No | No |
| *(default / package-private)* | Yes | Yes | No | No |
| **`protected`** | Yes | Yes | Yes | No |
| **`public`** | Yes | Yes | Yes | Yes |

---

# 3. Abstract Classes vs Interfaces

| Feature | Abstract Class (`abstract class`) | Interface (`interface`) |
| :--- | :--- | :--- |
| **State** | Can have instance variables with any visibility | Only `public static final` constants |
| **Multiple Inheritance** | A class can extend only **one** class | A class can implement **multiple** interfaces |
| **Constructor** | Has constructors (invoked by subclasses) | No constructors |
| **Method Types** | Abstract, concrete, final, static | Abstract, `default`, `static`, `private` |
| **Primary Intent** | Shared core code & identity among closely related classes | Behavioral capability contracts across unrelated classes |

### Modern Interface Features

Interfaces in modern Java can declare `default`, `static`, and `private` helper methods:

```java
public interface Logger {
    void log(String message); // Abstract contract

    // Default method (can be optionally overridden)
    default void logInfo(String info) {
        log(formatMessage("INFO", info));
    }

    default void logError(String error) {
        log(formatMessage("ERROR", error));
    }

    // Static utility method
    static Logger consoleLogger() {
        return System.out::println;
    }

    // Private helper method inside interface
    private String formatMessage(String level, String msg) {
        return "[%s] %s".formatted(level, msg);
    }
}
```

---

# 4. Sealed Classes and Interfaces

Sealed classes restrict which subclasses or sub-interfaces are permitted to extend or implement them, providing compile-time control over type hierarchies.

```java
// Sealed interface permits exactly three records
public sealed interface OrderState permits Pending, Paid, Shipped {}

public record Pending(String orderId) implements OrderState {}
public record Paid(String orderId, long paidTimestamp) implements OrderState {}
public record Shipped(String orderId, String trackingCode) implements OrderState {}
```

### Pattern Matching Exhaustiveness

Because the compiler knows every possible implementation of a sealed type, `switch` expressions do not require a redundant `default` branch:

```java
public static String describe(OrderState state) {
    return switch (state) {
        case Pending p  -> "Awaiting payment for " + p.orderId();
        case Paid p     -> "Payment received for " + p.orderId();
        case Shipped s  -> "Dispatched under tracking: " + s.trackingCode();
    };
}
```

---

# 5. Records

A `record` is a specialized, immutable class designed to model purely transparent data carriers.

```java
public record User(String id, String username, String email) {
    // Compact constructor: runs validation/sanitization before canonical initialization
    public User {
        if (username == null || username.isBlank()) {
            throw new IllegalArgumentException("Username required");
        }
        username = username.strip();
    }

    // Custom helper method
    public String displayTag() {
        return "%s <%s>".formatted(username, email);
    }
}
```

Features automatically generated by the compiler:
- `private final` fields for each component
- Canonical constructor
- Accessor methods (e.g., `user.username()`)
- `equals()` and `hashCode()` based on all fields
- Readable `toString()` output

---

# 6. Enums with State and Behavior

Enums in Java are full-fledged classes that can define fields, constructors, and methods.

```java
public enum Operation {
    PLUS("+") {
        @Override
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        @Override
        public double apply(double x, double y) { return x - y; }
    },
    MULTIPLY("*") {
        @Override
        public double apply(double x, double y) { return x * y; }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public String getSymbol() {
        return symbol;
    }

    public abstract double apply(double x, double y);
}

double total = Operation.MULTIPLY.apply(5, 4); // 20.0
```

---

# Summary

| Concept | Description |
| :--- | :--- |
| **Class** | Blueprint containing fields, constructors, and methods |
| **Object** | Concrete runtime instance of a class |
| **`extends`** | Single class inheritance keyword |
| **`implements`** | Interface implementation keyword (supports multiple) |
| **`@Override`** | Annotation enforcing valid runtime method override |
| **`super`** | Reference to the immediate parent class |
| **`sealed` / `permits`** | Restricts which types can inherit from a class or interface |
| **`record`** | Immutable, concise data-holding class |
