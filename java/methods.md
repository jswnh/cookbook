# 1. Methods

A method is a named block of code that performs an action, optionally takes parameters, and optionally returns a value.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

```java
public class Greeter {
    public static void greet() {
        System.out.println("Hello, World!");
    }

    public static void main(String[] args) {
        greet();
    }
}
```

Output:

```text
Hello, World!
```

---

# 2. Method Parameters

Methods can accept one or more typed input parameters.

```java
public static void greet(String name) {
    System.out.println("Hello, " + name + "!");
}

greet("Josuan");
```

Output:

```text
Hello, Josuan!
```

---

# 3. Returning Values

If a method specifies a return type other than `void`, it must return a compatible value along all code execution paths.

```java
public static int add(int a, int b) {
    return a + b;
}

int result = add(10, 20);
System.out.println(result); // 30
```

### Returning Multiple Values via Records

Java does not feature arbitrary tuple return types; instead, modern Java uses lightweight `record` definitions to return multiple named values with full type safety.

```java
public record MinMax(int min, int max) {}

public static MinMax findExtremes(int[] numbers) {
    int min = Integer.MAX_VALUE;
    int max = Integer.MIN_VALUE;

    for (int num : numbers) {
        if (num < min) min = num;
        if (num > max) max = num;
    }

    return new MinMax(min, max);
}

MinMax extremes = findExtremes(new int[]{ 4, 12, 1, 9, 25 });
System.out.printf("Min: %d, Max: %d%n", extremes.min(), extremes.max());
```

---

# 4. Method Overloading

Multiple methods within the same class can share the exact same name, as long as their parameter lists differ in count, types, or order.

```java
public class MathOperations {
    public static int multiply(int a, int b) {
        return a * b;
    }

    public static double multiply(double a, double b) {
        return a * b;
    }

    public static int multiply(int a, int b, int c) {
        return a * b * c;
    }
}
```

> [!NOTE]
> Return type alone cannot distinguish overloaded methods. Changing only the return type without modifying the parameter list causes a compile-time error.

---

# 5. Variable-Length Arguments (Varargs)

Varargs allows a method to accept zero or more arguments of a specified type, treated as an array inside the method body.

```java
public static int sumAll(int... numbers) {
    int total = 0;
    for (int n : numbers) {
        total += n;
    }
    return total;
}

System.out.println(sumAll());              // 0
System.out.println(sumAll(10, 20));        // 30
System.out.println(sumAll(1, 2, 3, 4, 5)); // 15
```

> [!WARNING]
> A method can have at most one varargs parameter, and it **must** be the last parameter in the signature.

---

# 6. Pass-by-Value Semantics

Java is **strictly pass-by-value**.
- For **primitives**, a copy of the actual binary value is passed.
- For **objects**, a copy of the object **reference address** is passed.

```java
public class ValueDemo {
    public static void modifyPrimitive(int num) {
        num = 999; // Modifies only local copy
    }

    public static void modifyObject(StringBuilder sb) {
        sb.append(" Modified"); // Mutates the referenced object on the heap
    }

    public static void reassignReference(StringBuilder sb) {
        sb = new StringBuilder("New Object"); // Rebinds local reference copy only
    }

    public static void main(String[] args) {
        int x = 10;
        modifyPrimitive(x);
        System.out.println(x); // 10 (unchanged)

        StringBuilder buffer = new StringBuilder("Original");
        modifyObject(buffer);
        System.out.println(buffer); // Original Modified

        reassignReference(buffer);
        System.out.println(buffer); // Original Modified (reference still points to original)
    }
}
```

---

# 7. Static vs Instance Methods

| Type | Declared With | Requires Instance | Can Access `this` | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Static Method** | `static` keyword | No (`ClassName.method()`) | No | Utility methods, pure math, factory constructors |
| **Instance Method**| Default | Yes (`obj.method()`) | Yes | Operations that read or mutate object state |

```java
public class Counter {
    private int count = 0; // Instance state

    // Instance method
    public void increment() {
        this.count++;
    }

    public int getCount() {
        return this.count;
    }

    // Static factory method
    public static Counter startingAt(int initial) {
        Counter c = new Counter();
        c.count = initial;
        return c;
    }
}
```

---

# 8. Functional Interfaces & Lambdas

A functional interface has exactly one abstract method (`SAM` - Single Abstract Method) and can be implemented cleanly with a lambda expression.

```java
import java.util.function.*;

// Predicate: T -> boolean
Predicate<Integer> isEven = n -> n % 2 == 0;
System.out.println(isEven.test(4)); // true

// Function: T -> R
Function<String, Integer> stringLength = s -> s.length();
System.out.println(stringLength.apply("Java 26")); // 7

// Consumer: T -> void
Consumer<String> printer = message -> System.out.println("[LOG] " + message);
printer.accept("Server healthy");

// Supplier: () -> T
Supplier<Double> randomValue = () -> Math.random();
System.out.println(randomValue.get());
```

---

# 9. Method References

Method references provide an even shorter, cleaner syntax for lambdas that simply invoke an existing method.

| Kind | Syntax | Lambda Equivalent |
| :--- | :--- | :--- |
| **Static Method** | `Math::abs` | `x -> Math.abs(x)` |
| **Instance Method of Object** | `System.out::println` | `x -> System.out.println(x)` |
| **Instance Method of Class** | `String::toUpperCase` | `s -> s.toUpperCase()` |
| **Constructor Reference** | `ArrayList::new` | `() -> new ArrayList<>()` |

```java
List<String> names = List.of("alice", "bob", "charlie");

// Using Class::instanceMethod
List<String> upper = names.stream()
                          .map(String::toUpperCase)
                          .toList();

// Using instance::instanceMethod
upper.forEach(System.out::println);
```

---

# 10. Modern Java Method Features (Java 22 to Java 26)

### Flexible Constructor Bodies (Statements Before `super()`)

Constructors can run validation, prepare arguments, and calculate values **before** delegating to `super(...)` or `this(...)`.

```java
public class BaseEntity {
    private final String id;

    public BaseEntity(String id) {
        this.id = id;
    }
}

public class ValidatedEntity extends BaseEntity {
    public ValidatedEntity(String rawId) {
        // Pre-super validation and normalization (allowed in Java 22+)
        if (rawId == null || rawId.isBlank()) {
            throw new IllegalArgumentException("ID cannot be empty");
        }
        String normalizedId = rawId.strip().toLowerCase();

        super(normalizedId);
    }
}
```

---

# 11. Real-World Example

Data pipeline with method validation, transformation, and method references.

```java
import java.util.List;
import java.util.function.Predicate;

public class OrderValidator {
    public record Order(String id, double amount, boolean isVerified) {}

    public static boolean isValidOrder(Order o) {
        return o != null && o.amount() > 0.0 && o.isVerified();
    }

    public static void main(String[] args) {
        List<Order> orders = List.of(
            new Order("O1", 250.0, true),
            new Order("O2", -10.0, true),
            new Order("O3", 120.0, false),
            new Order("O4", 450.0, true)
        );

        List<Order> approvedOrders = orders.stream()
            .filter(OrderValidator::isValidOrder)
            .toList();

        approvedOrders.forEach(o -> 
            System.out.printf("Approved: %s ($%.2f)%n", o.id(), o.amount())
        );
    }
}
```

Output:

```text
Approved: O1 ($250.00)
Approved: O4 ($450.00)
```

---

# Summary

| Concept | Key Syntax |
| :--- | :--- |
| **Standard Method** | `public static ReturnType methodName(Type param)` |
| **Varargs** | `void log(String... messages)` |
| **Overloading** | Same name, distinct parameter signatures |
| **Evaluation** | Strictly Pass-by-Value |
| **Lambda Expression** | `(params) -> expression` or `(params) -> { statements; }` |
| **Method Reference** | `ClassName::methodName` |
| **Multiple Return Values** | Model cleanly with a `record` |
