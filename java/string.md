# 1. Creating Strings

In Java, strings are instances of the `java.lang.String` class. Strings are immutable sequences of characters backed by compact byte arrays.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

## String Literal

String literals are stored in the **String Constant Pool** in JVM heap memory to save space.

```java
String firstName = "John";
String lastName = "Doe";
```

---

## Empty String

```java
String empty = "";
```

---

## Instantiation via `new`

Avoid using `new String(...)` directly unless explicitly required, as it bypasses the constant pool and forces new heap allocation.

```java
String explicit = new String("Not recommended");
```

---

# 2. String Immutability & Equality

Strings cannot be altered once created. Any method that appears to modify a string returns a brand new string instance.

## Equality: `.equals()` vs `==`

Always compare string content using `.equals()` or `.equalsIgnoreCase()`. The `==` operator compares reference memory locations, not content.

```java
String str1 = "Hello";
String str2 = new String("Hello");

System.out.println(str1 == str2);              // false (different heap references)
System.out.println(str1.equals(str2));          // true (identical character sequence)
System.out.println(str1.equalsIgnoreCase("HELLO")); // true
```

---

# 3. Text Blocks (Multi-Line Strings)

Text blocks (`"""`) allow clean multiline strings without tedious manual escape sequences (`\n`, `\"`).

```java
String jsonPayload = """
    {
        "id": "USR-4091",
        "username": "josuan",
        "active": true
    }
    """;

System.out.println(jsonPayload);
```

### Text Block Escape Sequences

- `\` at the end of a line suppresses the newline character (line continuation).
- `\s` explicitly preserves trailing whitespace that would otherwise be stripped.

```java
String query = """
    SELECT id, email, created_at \
    FROM users \
    WHERE status = 'ACTIVE'
    """;
```

---

# 4. String Formatting & Interpolation

## Using `.formatted()`

Modern Java provides the instance method `.formatted()` directly on `String`.

```java
String user = "Josuan";
int points = 1250;
double ratio = 98.675;

String summary = "User %s has %d pts (Pass rate: %.1f%%)".formatted(user, points, ratio);
System.out.println(summary);
```

Output:

```text
User Josuan has 1250 pts (Pass rate: 98.7%)
```

---

## Using `String.format()`

The traditional static formatting method:

```java
String output = String.format("System status: [%s] Code: %04d", "ONLINE", 7);
```

---

# 5. Common String Methods

## Inspection & Emptiness

```java
String text = "  Java 26  ";

// Length in characters
int len = text.length(); // 11

// Checks if length is 0
boolean empty = text.isEmpty(); // false

// Checks if string is empty or contains only whitespace characters
boolean blank = text.isBlank(); // false
System.out.println("   ".isBlank()); // true
```

---

## Stripping and Trimming

Prefer Unicode-aware `.strip()` methods over legacy `.trim()`.

```java
String raw = " \t Hello World \n ";

System.out.println("'" + raw.strip() + "'");         // 'Hello World'
System.out.println("'" + raw.stripLeading() + "'");  // 'Hello World \n '
System.out.println("'" + raw.stripTrailing() + "'"); // ' \t Hello World'
```

---

## Searching & Substrings

```java
String path = "src/main/java/App.java";

// Substring (start inclusive, end exclusive)
String filename = path.substring(12, 20); // App.java

// Slicing from index to end
String ext = path.substring(path.lastIndexOf(".")); // .java

// Verification
boolean hasJava = path.contains("java");    // true
boolean isJava = path.endsWith(".java");    // true
boolean inSrc = path.startsWith("src/");    // true

// Index lookup
int index = path.indexOf("main");           // 4
```

---

## Replacing

```java
String message = "The color is red. The sky is red.";

// Literal replacement of all occurrences
String updated = message.replace("red", "blue");

// Regex replacement
String sanitized = "User_123#456".replaceAll("[^A-Za-z0-9]", "-");
// Output: User_123-456
```

---

## Splitting and Joining

```java
// Splitting by delimiter regex
String tags = "java,spring,docker,cloud";
String[] tagArray = tags.split(",");

// Joining array or iterable with delimiter
String joined = String.join(" | ", tagArray);
System.out.println(joined); // java | spring | docker | cloud
```

---

## Modern Transformation Methods

```java
// Repeating
String divider = "=".repeat(20);

// Multi-line stream
String lines = "Row1\nRow2\nRow3";
lines.lines().forEach(line -> System.out.println("> " + line));

// Functional transformation
String result = "hello"
        .transform(String::toUpperCase)
        .transform(s -> "[" + s + "]");
System.out.println(result); // [HELLO]
```

---

# 6. High-Performance String Building

When concatenating strings inside loops or dynamic building pipelines, using `+` creates excessive intermediate objects. Use `StringBuilder` instead.

```java
StringBuilder builder = new StringBuilder();

for (int i = 1; i <= 5; i++) {
    builder.append("Batch-")
           .append(i)
           .append(" ");
}

String finalString = builder.toString();
System.out.println(finalString);
```

| Class | Thread Safety | Performance | Best For |
| :--- | :--- | :--- | :--- |
| **`String`** | Thread-safe (Immutable) | High for static expressions | Constants, API boundaries |
| **`StringBuilder`** | Not Thread-Safe | Maximum speed | Local concatenation loops |
| **`StringBuffer`** | Thread-safe (Synchronized) | Lower (locking overhead) | Legacy thread-shared buffers |

---

# 7. Real-World Example

Sanitizing, parsing, and validating user registration input.

```java
public class UserInputSanitizer {
    public record SanitizedUser(String username, String email, String domain) {}

    public static SanitizedUser process(String rawUsername, String rawEmail) {
        if (rawUsername == null || rawUsername.isBlank()) {
            throw new IllegalArgumentException("Username cannot be empty");
        }
        if (rawEmail == null || rawEmail.isBlank() || !rawEmail.contains("@")) {
            throw new IllegalArgumentException("Invalid email format");
        }

        String cleanUsername = rawUsername.strip().toLowerCase();
        String cleanEmail = rawEmail.strip().toLowerCase();

        String[] parts = cleanEmail.split("@", 2);
        String domain = parts[1];

        return new SanitizedUser(cleanUsername, cleanEmail, domain);
    }

    public static void main(String[] args) {
        SanitizedUser user = process("   JosuanDev  ", "  JOSUAN@example.COM  ");
        System.out.println(user);
        // Output: SanitizedUser[username=josuandev, email=josuan@example.com, domain=example.com]
    }
}
```

---

# Summary

| Task | Modern Java Method / Approach |
| :--- | :--- |
| **Equality** | `a.equals(b)` or `a.equalsIgnoreCase(b)` |
| **Multiline** | `"""` Text Block `"""` |
| **Interpolation** | `"... %s ...".formatted(arg)` |
| **Blankness check**| `str.isBlank()` (checks whitespace) |
| **Whitespace strip**| `str.strip()`, `str.stripLeading()`, `str.stripTrailing()` |
| **Search** | `str.contains(sub)`, `str.startsWith(prefix)`, `str.endsWith(suffix)` |
| **Multi-concatenation**| `StringBuilder` |
| **Repetition** | `str.repeat(n)` |
| **Lines to Stream**| `str.lines()` |
