# 1. Functions & Methods

A function is a named block of code. When defined inside a class, it is a method.

```php
function greet(): void {
    echo "Hello, World!\n";
}

greet();
```

Output:

```text
Hello, World!
```

---

# 2. Method Parameters

Methods can accept statically typed parameters.

```php
function greet(string $name): void {
    echo "Hello, {$name}!\n";
}

greet("John");
```

Output:

```text
Hello, John!
```

---

# 3. Returning Values

```php
function add(int $a, int $b): int {
    return $a + $b;
}

$result = add(10, 20);

echo $result . "\n";
```

Output:

```text
30
```

---

# 4. Arrow Functions (Expression-Bodied Methods)

Short, one-line functions capturing surrounding scope automatically (PHP 7.4+).

```php
$square = fn(int $number): int => $number * $number;

echo $square(5) . "\n";
```

Output:

```text
25
```

---

# 5. Optional Parameters

Provide default values for parameters.

```php
function greet(string $name, string $greeting = "Hello"): void {
    echo "{$greeting}, {$name}!\n";
}

greet("John");
greet("Jane", "Welcome");
```

---

# 6. Named Arguments (PHP 8.0+)

Enables passing arguments by parameter name, matching C# and improving readability.

```php
createUser(
    name: "John",
    age: 25,
    email: "john@example.com"
);

function createUser(string $name, int $age, string $email): void {
    echo $name . "\n";
}
```

---

# 7. Method Overloading

PHP does not natively support multiple method definitions with the same name. Instead, you use Union Types or variadic parameters.

```php
function add(int|float $a, int|float $b): int|float {
    return $a + $b;
}
```

---

# 8. Reference Parameters (C# `ref` equivalent)

Prepend `&` to pass arguments by reference.

```php
function increment(int &$value): void {
    $value++;
}

$number = 10;

increment($number);

echo $number . "\n";
```

Output:

```text
11
```

---

# 9. Returning Multiple Values (C# `out` equivalent)

You can pass reference parameters to act as outputs, or return an array and destructure it.

```php
function getUser(): array {
    $firstName = "John";
    $lastName = "Doe";
    return [$firstName, $lastName];
}

[$first, $last] = getUser();

echo "{$first} {$last}\n";
```

---

# 10. Variadic Parameters (C# `params` equivalent)

Accept a variable number of arguments using the `...` operator.

```php
function sum(int ...$numbers): int {
    return array_sum($numbers);
}

echo sum(1, 2, 3, 4, 5) . "\n";
```

Output:

```text
15
```

---

# 11. Nested / Local Functions (Closures)

You can define anonymous functions inside methods and bind parent variables using the `use` keyword.

```php
function process(): void {
    $factor = 2;

    $multiplier = function (int $a) use ($factor): int {
        return $a * $factor;
    };

    echo $multiplier(10) . "\n";
}

process();
```

---

# 12. Instance Methods

Require an object instance.

```php
class Calculator {
    public function add(int $a, int $b): int {
        return $a + $b;
    }
}

$calculator = new Calculator();

echo $calculator->add(10, 20) . "\n";
```

---

# 13. Static Methods

Belong to the class type namespace.

```php
class MathHelper {
    public static function double(int $value): int {
        return $value * 2;
    }
}

echo MathHelper::double(10) . "\n";
```

---

# 14. Accessors & Properties

PHP supports public fields, custom getter/setter methods, or intercepts via magic methods `__get` and `__set`.

## Constructor Property Promotion (Primary Constructor - PHP 8.0+)

Declares and initializes fields directly in the constructor signature, matching C# primary constructors.

```php
class User {
    public function __construct(
        public string $name = ""
    ) {}
}
```

---

## Readonly Property (PHP 8.1+)

Allows property writing only once (during initialization).

```php
class User {
    public function __construct(
        public readonly string $name
    ) {}
}
```

---

## Custom Getter and Setter

```php
class Product {
    private float $price = 0.0;

    public function getPrice(): float {
        return $this->price;
    }

    public function setPrice(float $value): void {
        if ($value < 0) {
            throw new InvalidArgumentException("Price cannot be negative.");
        }
        $this->price = $value;
    }
}
```

---

# 15. Readonly Classes (PHP 8.2+)

Enforces that the class itself is immutable and all its properties are implicitly `readonly`.

```php
readonly class User {
    public function __construct(
        public string $firstName,
        public string $lastName
    ) {}
}
```

---

# Summary

## Functions & Methods

- Named functions and methods
- Type hints (parameters and returns)
- Arrow functions (`fn() => ...`)
- Optional arguments and Named Arguments (`paramName: value`)
- Pass-by-reference using `&`
- Variadics using `...`
- Closures with scope binding (`use`)

## Properties

- Constructor Property Promotion (PHP 8.0+)
- Readonly properties (PHP 8.1+) and Readonly classes (PHP 8.2+)
- Getters/Setters

PHP combines classical object-oriented parameter patterns with modern constructor promotion and strict typing to match compile-time safety paradigms of managed platforms.
