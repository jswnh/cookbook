# 1. Core Generic Type Definitions

Generics enforce compile-time type safety, eliminate manual casts, and enable reusable algorithms across diverse data types.

Target Environment: **Java SE 26 (JDK 26.0.2.1)**

## Generic Classes

A class declared with one or more type parameters acting as placeholders.

```java
public class Box<T> {
    private T item;

    public void set(T item) {
        this.item = item;
    }

    public T get() {
        return item;
    }
}

Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String text = stringBox.get(); // No cast needed
```

---

## Generic Interfaces

```java
public interface Repository<T, ID> {
    void save(T entity);
    T findById(ID id);
}
```

---

## Generic Methods

A method can declare its own type parameters independent of its enclosing class. The type parameter is placed before the return type.

```java
public class ArrayUtilities {
    public static <T> void swap(T[] array, int i, int j) {
        T temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

---

## Generic Records

```java
public record ApiResponse<T>(int statusCode, String message, T data) {}

ApiResponse<User> response = new ApiResponse<>(200, "OK", new User("101", "Josuan"));
```

---

# 2. Type Parameter Naming Conventions

By convention, type parameters are single uppercase letters:

| Parameter | Meaning | Standard Context |
| :--- | :--- | :--- |
| **`T`** | Type | General type placeholder |
| **`E`** | Element | Collections (`List<E>`, `Set<E>`) |
| **`K`** | Key | Maps (`Map<K, V>`) |
| **`V`** | Value | Maps and return values |
| **`N`** | Number | Numeric calculations |
| **`R`** | Result | Functions (`Function<T, R>`) |

---

# 3. Bounded Type Parameters

Bounds restrict the types that can be supplied as arguments for a type parameter.

## Upper Bound (`extends`)

Restricts the type argument to a specific class or its subclasses / implementers.

```java
public class Stats<T extends Number> {
    private final T[] numbers;

    public Stats(T[] numbers) {
        this.numbers = numbers;
    }

    public double average() {
        double sum = 0.0;
        for (T num : numbers) {
            // Can safely call doubleValue() because T is bounded to Number
            sum += num.doubleValue();
        }
        return sum / numbers.length;
    }
}
```

---

## Multiple Bounds (`&`)

A type can be bounded by a class and multiple interfaces. The class (if present) **must** come first.

```java
public static <T extends Comparable<T> & java.io.Serializable> T max(T a, T b) {
    return (a.compareTo(b) >= 0) ? a : b;
}
```

---

# 4. Wildcards & Generic Variance (PECS)

In Java, generic types are **invariant**: `List<String>` is **not** a subtype of `List<Object>`. Wildcards (`?`) introduce variance.

## The PECS Principle: Producer Extends, Consumer Super

- **Producer Extends**: If your method **reads** items from a collection, use `? extends T` (Covariant).
- **Consumer Super**: If your method **writes** items to a collection, use `? super T` (Contravariant).

```java
import java.util.ArrayList;
import java.util.List;

public class CollectionsHelper {

    // PRODUCER: reads items from 'source' to compute sum (Upper-bounded wildcard)
    public static double sumNumbers(List<? extends Number> source) {
        double total = 0.0;
        for (Number num : source) {
            total += num.doubleValue();
        }
        return total;
    }

    // CONSUMER: writes integers into 'destination' (Lower-bounded wildcard)
    public static void addIntegers(List<? super Integer> destination) {
        for (int i = 1; i <= 5; i++) {
            destination.add(i);
        }
    }

    // PRODUCER-CONSUMER COPY: reads from src, writes into dest
    public static <T> void copy(List<? super T> dest, List<? extends T> src) {
        for (T item : src) {
            dest.add(item);
        }
    }
}
```

Usage:

```java
List<Integer> intList = List.of(10, 20, 30);
double sum = CollectionsHelper.sumNumbers(intList); // Valid because Integer extends Number

List<Number> numList = new ArrayList<>();
CollectionsHelper.addIntegers(numList); // Valid because Number is a supertype of Integer
```

---

# 5. Type Erasure & JVM Limitations

Java implements generics via **Type Erasure**. The compiler enforces type safety at compile-time and then erases all generic type parameters in bytecode (replacing unbounded types with `Object` and bounded types with their upper bound).

### What Type Erasure Prohibits

```java
public class Restrictions<T> {
    // 1. CANNOT instantiate a type parameter directly
    // T obj = new T(); // COMPILE ERROR

    // 2. CANNOT allocate a generic array directly
    // T[] arr = new T[10]; // COMPILE ERROR

    // 3. CANNOT declare static fields with class type parameters
    // private static T shared; // COMPILE ERROR

    // 4. CANNOT use instanceof with parameterized types
    // if (obj instanceof List<String>) {} // COMPILE ERROR
}
```

### Idiomatic Workarounds

Using a `Class<T>` type token or a `Supplier<T>` to instantiate types dynamically:

```java
import java.util.function.Supplier;

public class Factory<T> {
    public static <T> T createInstance(Supplier<T> supplier) {
        return supplier.get();
    }
}

String instance = Factory.createInstance(String::new);
```

---

# 6. Real-World Example

A type-safe generic memory store supporting bounded items and bulk transfers.

```java
import java.util.*;

public class CacheStore<ID, T> {
    private final Map<ID, T> store = new HashMap<>();

    public void put(ID id, T item) {
        store.put(id, item);
    }

    public Optional<T> get(ID id) {
        return Optional.ofNullable(store.get(id));
    }

    // Consumer super: loads all values into a collector list
    public void dumpInto(List<? super T> targetList) {
        targetList.addAll(store.values());
    }

    public static void main(String[] args) {
        CacheStore<String, Integer> cache = new CacheStore<>();
        cache.put("sensor-1", 42);
        cache.put("sensor-2", 89);

        List<Number> results = new ArrayList<>();
        cache.dumpInto(results); // Number is super of Integer

        System.out.println("Cached elements: " + results);
    }
}
```

---

# Summary

| Concept | Syntax | Meaning |
| :--- | :--- | :--- |
| **Generic Class** | `class Name<T> { }` | Parameterized class |
| **Generic Method**| `<T> T methodName(T arg)` | Method with independent type parameter |
| **Upper Bound** | `<T extends Number>` | `T` must be or extend `Number` |
| **Multiple Bounds**| `<T extends A & B>` | `T` must satisfy both contracts |
| **Covariant Read**| `? extends T` | Read-only producer collection |
| **Contravariant Write**| `? super T` | Writeable consumer collection |
| **PECS Rule** | Producer Extends, Consumer Super | Guideline for API wildcard flexibility |
