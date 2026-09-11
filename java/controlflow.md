# 1. Conditional Statements

Conditional statements execute code blocks based on boolean conditions.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

## if

Execute code when a condition evaluates to `true`.

```java
int age = 18;

if (age >= 18) {
    System.out.println("Adult");
}
```

---

## if-else

Execute one branch when `true`, and an alternate branch when `false`.

```java
int age = 16;

if (age >= 18) {
    System.out.println("Adult");
} else {
    System.out.println("Minor");
}
```

---

## if-else if

Chain multiple conditions sequentially.

```java
int score = 85;

if (score >= 90) {
    System.out.println("Grade: A");
} else if (score >= 80) {
    System.out.println("Grade: B");
} else if (score >= 70) {
    System.out.println("Grade: C");
} else {
    System.out.println("Grade: F");
}
```

---

## Ternary Operator

Compact inline conditional expression (`condition ? exprIfTrue : exprIfFalse`).

```java
int age = 20;
String status = (age >= 18) ? "Eligible" : "Ineligible";

System.out.println(status);
```

---

# 2. Switch Statement (Traditional)

Traditional switch statements require explicit `break` statements to prevent fall-through.

```java
String role = "ADMIN";

switch (role) {
    case "ADMIN":
        System.out.println("Full system access granted");
        break;

    case "MANAGER":
        System.out.println("Resource management access");
        break;

    case "USER":
        System.out.println("Standard access");
        break;

    default:
        System.out.println("Unknown role");
        break;
}
```

---

# 3. Switch Expressions (Modern Java)

Modern Java favors switch expressions using the arrow (`->`) syntax. Arrow labels eliminate accidental fall-through and allow switch to return a value directly.

```java
String role = "ADMIN";

String accessLevel = switch (role) {
    case "ADMIN" -> "Full Access";
    case "MANAGER" -> "Manage Resources";
    case "USER" -> "Limited Access";
    default -> "Unknown Role";
};

System.out.println(accessLevel);
```

### Multiple Case Labels

```java
import java.time.DayOfWeek;
import java.time.LocalDate;

DayOfWeek today = LocalDate.now().getDayOfWeek();

String dayType = switch (today) {
    case SATURDAY, SUNDAY -> "Weekend";
    case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> "Weekday";
};

System.out.println(dayType);
```

### Multi-Line Blocks with `yield`

When a branch requires multiple statements, use a code block and return the value using `yield`:

```java
int code = 404;

String message = switch (code) {
    case 200 -> "OK";
    case 404 -> {
        System.err.println("Resource missing: logging telemetry");
        yield "Not Found";
    }
    case 500 -> {
        System.err.println("Critical server exception");
        yield "Internal Server Error";
    }
    default -> "Unhandled Status Code";
};
```

---

# 4. Pattern Matching for switch

Java 26 features exhaustive pattern matching in `switch`, allowing direct type inspection, extraction, and guarded expressions with `when`.

```java
public static String formatValue(Object obj) {
    return switch (obj) {
        case Integer i when i > 100 -> "Large integer: " + i;
        case Integer i              -> "Standard integer: " + i;
        case Long l                 -> "64-bit integer: " + l;
        case Double d               -> "Floating-point: " + d;
        case String s when !s.isBlank() -> "Text content: " + s.strip();
        case String _               -> "Blank or empty string";
        case null                   -> "Null reference";
        default                     -> "Unknown object: " + obj;
    };
}
```

---

# 5. Record Patterns & Unnamed Patterns

Java 26 supports destructuring record components directly in `switch` patterns and using unnamed pattern variables (`_`) when specific components are unused.

```java
public sealed interface Shape permits Circle, Rectangle {}
public record Circle(double radius) implements Shape {}
public record Rectangle(double width, double height) implements Shape {}

public static double calculateArea(Shape shape) {
    return switch (shape) {
        case Circle(double r)            -> Math.PI * r * r;
        case Rectangle(double w, double h) -> w * h;
    };
}
```

### Unnamed Variables and Patterns (`_`)

```java
record Transaction(String id, double amount, String currency) {}

public static void logTransaction(Transaction tx) {
    // Destructure only amount, ignoring id and currency
    if (tx instanceof Transaction(_, double amount, _)) {
        System.out.println("Processing transaction amount: " + amount);
    }
}
```

---

# 6. Loops

## for Loop

Best when iteration count is known in advance.

```java
for (int i = 1; i <= 5; i++) {
    System.out.println("Iteration: " + i);
}
```

---

## Enhanced for-each Loop

Preferred for iterating over arrays and any class implementing `java.lang.Iterable<T>`.

```java
String[] frameworks = { "Spring Boot", "Quarkus", "Micronaut" };

for (String framework : frameworks) {
    System.out.println(framework);
}
```

---

## while Loop

Executes as long as the condition remains `true`.

```java
int count = 1;

while (count <= 3) {
    System.out.println("Count: " + count);
    count++;
}
```

---

## do-while Loop

Guaranteed to execute at least once before checking the condition.

```java
int attempts = 0;

do {
    System.out.println("Attempt: " + attempts);
    attempts++;
} while (attempts < 3);
```

---

# 7. Loop Control Statements

## break

Immediately terminates the loop.

```java
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        break;
    }
    System.out.print(i + " ");
}
// Output: 1 2 3 4
```

---

## continue

Skips the remainder of the current iteration and advances to the next.

```java
for (int i = 1; i <= 5; i++) {
    if (i % 2 == 0) {
        continue;
    }
    System.out.print(i + " ");
}
// Output: 1 3 5
```

---

## Labeled break and continue

Java allows labeling loops to break or continue outer loops from nested inner scopes.

```java
outerLoop:
for (int row = 0; row < 3; row++) {
    for (int col = 0; col < 3; col++) {
        if (row == 1 && col == 1) {
            System.out.println("Target found at (1,1). Breaking outer loop.");
            break outerLoop;
        }
        System.out.printf("[%d,%d] ", row, col);
    }
}
```

---

# 8. Nested Loops

Commonly used for multidimensional matrices and grid coordinate traversal.

```java
int[][] grid = {
    { 1, 2, 3 },
    { 4, 5, 6 }
};

for (int r = 0; r < grid.length; r++) {
    for (int c = 0; c < grid[r].length; c++) {
        System.out.print(grid[r][c] + " ");
    }
    System.out.println();
}
```

---

# 9. Real-World Example

Combining records, pattern matching switch expressions, and loops to process an order event stream.

```java
import java.util.List;

public class OrderProcessor {
    public sealed interface OrderEvent permits Placed, Shipped, Cancelled {}
    public record Placed(String orderId, double total) implements OrderEvent {}
    public record Shipped(String orderId, String trackingNumber) implements OrderEvent {}
    public record Cancelled(String orderId, String reason) implements OrderEvent {}

    public static void main(String[] args) {
        List<OrderEvent> events = List.of(
            new Placed("ORD-101", 149.99),
            new Shipped("ORD-101", "TRK-992014"),
            new Cancelled("ORD-102", "Customer requested cancellation")
        );

        for (OrderEvent event : events) {
            String logEntry = switch (event) {
                case Placed(String id, double total) when total > 100.0 ->
                    "High-value order placed: %s ($%.2f)".formatted(id, total);
                case Placed(String id, double total) ->
                    "Standard order placed: %s ($%.2f)".formatted(id, total);
                case Shipped(String id, String trk) ->
                    "Order %s in transit with tracking: %s".formatted(id, trk);
                case Cancelled(String id, String reason) ->
                    "Order %s cancelled. Reason: %s".formatted(id, reason);
            };

            System.out.println(logEntry);
        }
    }
}
```

Output:

```text
High-value order placed: ORD-101 ($149.99)
Order ORD-101 in transit with tracking: TRK-992014
Order ORD-102 cancelled. Reason: Customer requested cancellation
```

---

# Summary

## Conditional Constructs

| Construct | Description |
| :--- | :--- |
| **`if / else if / else`** | Standard boolean branching |
| **`? :`** | Ternary conditional operator |
| **Switch statement** | Traditional multi-branch statement (uses `break`) |
| **Switch expression** | Modern value-returning switch using `->` and `yield` |
| **Pattern matching switch** | Type testing, pattern guards (`when`), and record destructuring |

## Iteration Constructs

| Construct | Primary Use Case |
| :--- | :--- |
| **`for (init; cond; step)`** | Indexed counter iterations |
| **`for (T item : collection)`**| Sequential traversal of arrays and iterables |
| **`while (cond)`** | Loop condition evaluated before each iteration |
| **`do ... while (cond)`** | Loop body runs at least once before condition evaluation |
| **`break` / `continue`** | Control statements (supports labeled targets) |
