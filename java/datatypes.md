## 1. Primitive Types (Value Types)

Java is a statically typed language. Primitive types are predefined by the language and store raw binary values directly in memory (on the call stack or inline within objects), without object overhead.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

### Integral Types (Whole Numbers)

| Keyword    | Wrapper Class        | Size   | Range                                                    | Default | Example                                |
| :--------- | :------------------- | :----- | :------------------------------------------------------- | :------ | :------------------------------------- |
| **`byte`** | `java.lang.Byte`     | 8-bit  | -128 to 127                                              | `0`     | `byte level = 12;`                     |
| **`short`**| `java.lang.Short`    | 16-bit | -32,768 to 32,767                                        | `0`     | `short port = 8080;`                   |
| **`int`**  | `java.lang.Integer`  | 32-bit | -2,147,483,648 to 2,147,483,647                          | `0`     | `int totalCount = 1_500_000;`          |
| **`long`** | `java.lang.Long`     | 64-bit | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807  | `0L`    | `long timestamp = 1718000000000L;`     |

> [!NOTE]
> Numeric literals support underscores (`_`) as visual digit separators (e.g., `1_000_000`), which improves readability without affecting the value.

### Floating-Point Types (Fractional Numbers)

| Keyword      | Wrapper Class       | Size   | Precision       | Default | Example                               |
| :----------- | :------------------ | :----- | :-------------- | :------ | :------------------------------------ |
| **`float`**  | `java.lang.Float`   | 32-bit | ~6-7 digits     | `0.0f`  | `float ratio = 3.14159f;`             |
| **`double`** | `java.lang.Double`  | 64-bit | ~15-17 digits   | `0.0d`  | `double latitude = 37.7749295;`       |

> [!TIP]
> For financial and high-precision calculations where rounding errors cannot be tolerated, always use `java.math.BigDecimal` instead of `float` or `double`.

### Other Primitive Types

| Keyword       | Wrapper Class         | Size   | Values / Range                          | Default | Example                                |
| :------------ | :-------------------- | :----- | :-------------------------------------- | :------ | :------------------------------------- |
| **`char`**    | `java.lang.Character` | 16-bit | `'\u0000'` (0) to `'\uffff'` (65,535)   | `'\u0000'` | `char status = 'A';`                |
| **`boolean`** | `java.lang.Boolean`   | 1-bit* | `true` or `false`                       | `false` | `boolean isReady = true;`              |

*\* The JVM specification does not define an explicit size for `boolean`; it is typically represented using 8-bit bytes in memory, or 32-bit integers in array slots.*

---

## 2. Autoboxing and Unboxing

Autoboxing is the automatic conversion the Java compiler makes between primitive types and their corresponding object wrapper classes. Unboxing is the reverse conversion.

```java
// Autoboxing: primitive int to Integer object
Integer boxed = 42;

// Unboxing: Integer object back to primitive int
int unboxed = boxed;

// In Collections (Collections require objects, not primitives)
List<Integer> numbers = new ArrayList<>();
numbers.add(10); // Automatically boxed to Integer.valueOf(10)
int first = numbers.get(0); // Automatically unboxed via intValue()
```

> [!WARNING]
> Autoboxing inside tight computational loops introduces heap allocation and garbage collection pressure. Additionally, unboxing a `null` wrapper reference throws a `NullPointerException`.

---

## 3. Reference Types

Reference types store memory addresses (references) that point to objects allocated on the JVM heap.

| Type                | Description                                                               | Example                                              |
| :------------------ | :------------------------------------------------------------------------ | :--------------------------------------------------- |
| **`String`**        | Immutable sequence of UTF-16 characters backed by compact byte arrays     | `String message = "Hello, Java 26!";`                |
| **`Object`**        | The root class in the Java class hierarchy                                | `Object payload = new Customer("Josuan");`           |
| **Arrays (`T[]`)**  | Fixed-size contiguous sequences of elements                               | `int[] scores = { 95, 88, 72 };`                     |
| **`class`**         | User-defined blueprints encapsulating state and methods                   | `public class Account { private double balance; }`   |
| **`interface`**     | Contracts specifying abstract methods, default methods, and static members| `public interface Serializable { }`                  |
| **`enum`**          | Specialized classes representing a fixed set of named constants           | `public enum Role { ADMIN, USER, GUEST }`            |
| **`record`**        | Transparent, immutable data carriers with concise syntax                 | `public record User(String id, String email) {}`     |

---

## 4. Modern Type Features (Java 26 / JDK 26.0.2.1)

### Local Variable Type Inference (`var`)

Introduced to reduce boilerplate while maintaining strict compile-time type safety. The compiler infers the concrete type from the right-hand assignment expression.

```java
// Type inferred as java.lang.String
var greeting = "Welcome to Java 26.0.2.1";

// Type inferred as java.util.ArrayList<java.lang.String>
var names = new ArrayList<String>();

// Type inferred as java.util.Map<java.lang.String, java.util.List<java.lang.Integer>>
var scoresByTeam = Map.of("Devs", List.of(100, 95));
```

> [!NOTE]
> `var` cannot be used for class fields, method parameter types, or uninitialized variables. It is strictly for local variables inside methods, loops, and resource blocks.

### Records (Immutable Data Carriers)

Records produce an immutable class with private final fields, a canonical constructor, getters, `equals()`, `hashCode()`, and `toString()` automatically generated.

```java
public record Coordinates(double latitude, double longitude) {
    // Compact constructor for validation
    public Coordinates {
        if (latitude < -90.0 || latitude > 90.0) {
            throw new IllegalArgumentException("Invalid latitude");
        }
    }
}

Coordinates location = new Coordinates(14.5995, 120.9842);
System.out.println(location.latitude()); // Accessor method
```

### Primitive Patterns & Type Narrowing

In Java 26, pattern matching works smoothly with both reference and primitive types across `instanceof` and `switch` expressions:

```java
public static void inspect(Object obj) {
    if (obj instanceof byte b) {
        System.out.println("Byte value: " + b);
    } else if (obj instanceof int i) {
        System.out.println("Integer value: " + i);
    } else if (obj instanceof String s) {
        System.out.println("String of length: " + s.length());
    }
}
```

### Null Safety Patterns

Java reference types can be `null`. Modern Java encourages defensive programming using `java.util.Objects` and `java.util.Optional<T>`:

```java
import java.util.Objects;
import java.util.Optional;

public class ProfileService {
    public void register(String username, String email) {
        // Enforce non-null arguments at boundary
        String validName = Objects.requireNonNull(username, "Username must not be null");
        String validEmail = Objects.requireNonNull(email, "Email must not be null");
    }

    public Optional<String> findNickname(String userId) {
        String nickname = fetchFromDatabase(userId);
        return Optional.ofNullable(nickname);
    }

    private String fetchFromDatabase(String id) {
        return null;
    }
}
```
