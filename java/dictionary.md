# 1. Creating a Map (Dictionary)

In Java, key-value mappings are represented by the `java.util.Map<K, V>` interface. A Map cannot contain duplicate keys; each key maps to at most one value.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

## Mutable HashMap (Standard)

`HashMap` provides $O(1)$ average time complexity for basic operations.

```java
import java.util.HashMap;
import java.util.Map;

Map<Integer, String> users = new HashMap<>();
users.put(1, "John");
users.put(2, "Jane");
users.put(3, "Bob");
```

---

## Immutable Map Factory Methods

Modern Java provides clean factory methods for creating unmodifiable, non-null maps:

```java
import java.util.Map;
import static java.util.Map.entry;

// Up to 10 entries
Map<Integer, String> quickMap = Map.of(
    1, "John",
    2, "Jane",
    3, "Bob"
);

// Arbitrary number of entries using entry()
Map<String, Integer> config = Map.ofEntries(
    entry("port", 8080),
    entry("timeout", 5000),
    entry("maxConnections", 100)
);
```

> [!CAUTION]
> Maps created via `Map.of()` and `Map.ofEntries()` are strictly immutable and do not allow `null` keys or values. Calling `put()` or passing `null` throws `UnsupportedOperationException` or `NullPointerException`.

---

## Implementations at a Glance

| Implementation | Ordering | Time Complexity | Null Allowed | Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **`HashMap`** | None (Unordered) | $O(1)$ | 1 `null` key, multiple `null` values | Default general-purpose choice |
| **`LinkedHashMap`**| Insertion or Access Order | $O(1)$ | Yes | Predictable iteration, LRU caches |
| **`TreeMap`** | Natural sorted or `Comparator` | $O(\log n)$ | No `null` keys | Sorted keys, range queries |
| **`ConcurrentHashMap`**| None | $O(1)$ | No `null` keys or values | High-throughput concurrent multi-threading |

---

# 2. Adding and Updating Items

```java
Map<String, String> capitals = new HashMap<>();

// Put (adds or overwrites)
capitals.put("USA", "Washington D.C.");
capitals.put("Japan", "Tokyo");

// Put only if key does not exist yet
capitals.putIfAbsent("France", "Paris");
capitals.putIfAbsent("USA", "New York"); // Ignored because "USA" exists

// Explicit replacement
capitals.replace("Japan", "Tokyo Metropolitan");

// Conditional replacement (replaces only if current value matches expected)
capitals.replace("France", "Lyon", "Paris"); // No change
```

---

# 3. Accessing Values

```java
Map<String, Integer> inventory = new HashMap<>();
inventory.put("Apples", 50);
inventory.put("Oranges", 30);

// Basic get (returns null if key not present)
Integer appleCount = inventory.get("Apples"); // 50

// Get with fallback default
int bananaCount = inventory.getOrDefault("Bananas", 0); // 0
```

---

# 4. Removing Items

```java
Map<String, String> statusMap = new HashMap<>();
statusMap.put("SVC-1", "HEALTHY");
statusMap.put("SVC-2", "DEGRADED");

// Remove by key
statusMap.remove("SVC-1");

// Conditional remove: only removes if key maps to specific value
statusMap.remove("SVC-2", "HEALTHY"); // Not removed because value is DEGRADED
statusMap.remove("SVC-2", "DEGRADED"); // Removed

// Clear all entries
statusMap.clear();
```

---

# 5. Checking Keys, Values, and Size

```java
Map<String, Double> prices = Map.of(
    "Laptop", 1299.99,
    "Mouse", 29.99
);

boolean hasLaptop = prices.containsKey("Laptop"); // true
boolean hasFreeItem = prices.containsValue(0.0);   // false

int totalItems = prices.size();      // 2
boolean empty = prices.isEmpty();     // false
```

---

# 6. Iterating Over Maps

## Iterating Key-Value Pairs (`entrySet`)

The most efficient approach when both keys and values are needed.

```java
Map<String, Integer> scores = Map.of("Alice", 95, "Bob", 88);

for (Map.Entry<String, Integer> entry : scores.entrySet()) {
    System.out.printf("%s : %d%n", entry.getKey(), entry.getValue());
}
```

---

## Iterating Keys Only

```java
for (String student : scores.keySet()) {
    System.out.println("Student: " + student);
}
```

---

## Iterating Values Only

```java
for (int score : scores.values()) {
    System.out.println("Score: " + score);
}
```

---

## Functional `forEach` Method

```java
scores.forEach((name, score) -> {
    System.out.println(name + " -> " + score);
});
```

---

# 7. Advanced Functional Operations

Modern Java maps provide atomic and functional mutating methods that eliminate verbose null-checking boilerplate.

## `computeIfAbsent`

Calculates a value and inserts it only when the key is not present.

```java
Map<String, List<String>> categorisedSkills = new HashMap<>();

// Seamlessly initializes nested lists
categorisedSkills.computeIfAbsent("Backend", k -> new ArrayList<>()).add("Java 26");
categorisedSkills.computeIfAbsent("Backend", k -> new ArrayList<>()).add("PostgreSQL");
```

---

## `merge`

Combines an existing value with a new value using a bifunction.

```java
Map<String, Integer> wordFrequencies = new HashMap<>();

String[] words = { "apple", "banana", "apple", "cherry", "apple" };

for (String word : words) {
    // If key absent, sets 1; otherwise executes (oldVal + 1)
    wordFrequencies.merge(word, 1, Integer::sum);
}

System.out.println(wordFrequencies); 
// Output: {banana=1, apple=3, cherry=1}
```

---

# 8. SequencedMap (Java 21 to Java 26)

`SequencedMap` provides first/last element access and reversed views for ordered maps (`LinkedHashMap`, `TreeMap`).

```java
import java.util.LinkedHashMap;
import java.util.SequencedMap;

SequencedMap<Integer, String> timeline = new LinkedHashMap<>();
timeline.put(1, "Genesis");
timeline.put(2, "Milestone A");
timeline.put(3, "Milestone B");

// Direct first and last access
System.out.println(timeline.firstEntry()); // 1=Genesis
System.out.println(timeline.lastEntry());  // 3=Milestone B

// Reverse iteration view without copying
SequencedMap<Integer, String> reversed = timeline.reversed();
reversed.forEach((k, v) -> System.out.println(k + ": " + v));
```

---

# 9. Concurrent Map (`ConcurrentHashMap`)

High-throughput thread-safe map without synchronizing the entire table.

```java
import java.util.concurrent.ConcurrentHashMap;

ConcurrentHashMap<String, Long> activeSessions = new ConcurrentHashMap<>();

activeSessions.put("USR-01", System.currentTimeMillis());
activeSessions.computeIfAbsent("USR-02", k -> System.currentTimeMillis());
```

---

# 10. Real-World Example

Implementing a frequency counter and grade classifier.

```java
import java.util.*;

public class GradeAnalytics {
    public record Student(String name, int score) {}

    public static void main(String[] args) {
        List<Student> students = List.of(
            new Student("Alice", 92),
            new Student("Bob", 78),
            new Student("Charlie", 95),
            new Student("Diana", 83),
            new Student("Evan", 74)
        );

        Map<String, List<String>> studentsByGrade = new TreeMap<>();

        for (Student s : students) {
            String grade = switch (s.score() / 10) {
                case 10, 9 -> "Grade A";
                case 8     -> "Grade B";
                case 7     -> "Grade C";
                default    -> "Grade F";
            };

            studentsByGrade
                .computeIfAbsent(grade, _ -> new ArrayList<>())
                .add(s.name());
        }

        studentsByGrade.forEach((grade, names) -> {
            System.out.printf("%s: %s%n", grade, String.join(", ", names));
        });
    }
}
```

Output:

```text
Grade A: Alice, Charlie
Grade B: Diana
Grade C: Bob, Evan
```

---

# Summary

| Method | Purpose |
| :--- | :--- |
| **`Map.of(k1, v1, ...)`** | Create an immutable, non-null map |
| **`put(key, value)`** | Insert or overwrite key-value pair |
| **`putIfAbsent(key, value)`**| Insert only if key does not exist |
| **`get(key)`** | Retrieve value or `null` |
| **`getOrDefault(key, default)`**| Retrieve value or fallback default |
| **`containsKey(key)`** | Check if key exists |
| **`entrySet()`** | Set of key-value pairs for iteration |
| **`computeIfAbsent(k, func)`**| Compute value on-demand if absent |
| **`merge(k, val, bifunc)`** | Combine existing value with new value |
| **`SequencedMap`** | First/last access (`firstEntry()`, `reversed()`) |
