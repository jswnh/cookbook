# 1. Conditional Statements

## if

Execute code when a condition is true.

```php
$age = 18;

if ($age >= 18) {
    echo "Adult\n";
}
```

---

## if-else

```php
$age = 16;

if ($age >= 18) {
    echo "Adult\n";
} else {
    echo "Minor\n";
}
```

---

## if-elseif-else

```php
$score = 85;

if ($score >= 90) {
    echo "A\n";
} elseif ($score >= 80) {
    echo "B\n";
} elseif ($score >= 70) {
    echo "C\n";
} else {
    echo "Failed\n";
}
```

---

## Switch Statement

Traditional multi-branch check (uses loose comparison `==`).

```php
$role = "Admin";

switch ($role) {
    case "Admin":
        echo "Full Access\n";
        break;

    case "Manager":
        echo "Manage Resources\n";
        break;

    case "User":
        echo "Limited Access\n";
        break;

    default:
        echo "Unknown Role\n";
        break;
}
```

### Multiple Cases

```php
$day = date('w'); // 0 = Sunday, 6 = Saturday

switch ($day) {
    case 0:
    case 6:
        echo "Weekend\n";
        break;

    default:
        echo "Weekday\n";
        break;
}
```

---

## Match Expression (Modern PHP 8.0+)

The `match` expression is the modern preferred alternative to `switch`. It performs strict type comparisons (`===`), returns a value directly, does not require `break` statements (prevents fall-through bugs), and requires exhaustive matching (throws `UnhandledMatchError` if unmatched).

```php
$role = "Admin";

$access = match ($role) {
    "Admin" => "Full Access",
    "Manager" => "Manage Resources",
    "User" => "Limited Access",
    default => "Unknown Role",
};

echo $access . "\n";
```

### Pattern Matching / Guards with Match

```php
$value = 150;

$result = match (true) {
    is_int($value) && $value > 100 => "Large Number",
    is_int($value) => "Small Number",
    is_string($value) => "Text",
    is_null($value) => "Null",
    default => "Unknown",
};

echo $result . "\n";
```

---

# 2. Loops

## for

Best when the iteration count is known.

```php
for ($i = 1; $i <= 5; $i++) {
    echo $i . "\n";
}
```

---

## foreach

Preferred for iterating arrays and iterables.

```php
$languages = ["C#", "TypeScript", "PHP"];

foreach ($languages as $language) {
    echo $language . "\n";
}
```

With keys:

```php
foreach ($languages as $index => $language) {
    echo "{$index}: {$language}\n";
}
```

---

## while

```php
$count = 1;

while ($count <= 5) {
    echo $count . "\n";
    $count++;
}
```

---

## do-while

Runs at least once.

```php
$count = 1;

do {
    echo $count . "\n";
    $count++;
} while ($count <= 5);
```

---

# 3. Loop Control Statements

## break

Stops execution of a loop. Supports breaking out of multiple nested loops (e.g. `break 2`).

```php
for ($i = 1; $i <= 10; $i++) {
    if ($i === 5) {
        break;
    }
    echo $i . "\n";
}
```

---

## continue

Skips the current iteration. Supports skipping multiple loop levels (e.g. `continue 2`).

```php
for ($i = 1; $i <= 5; $i++) {
    if ($i === 3) {
        continue;
    }
    echo $i . "\n";
}
```

---

# 4. Arrays & Lists

PHP uses unified arrays for ordered list and key-value mapping.

```php
$numbers = [10, 20, 30, 40, 50];

foreach ($numbers as $number) {
    echo $number . "\n";
}
```

---

# 5. Dictionaries / Associative Arrays

```php
$users = [
    1 => "John",
    2 => "Jane",
    3 => "Bob"
];

foreach ($users as $id => $name) {
    echo "{$id}: {$name}\n";
}
```

---

# 6. SPL Data Structures

PHP includes the Standard PHP Library (SPL) containing specialized, memory-optimized data structures.

## SplQueue (FIFO)

```php
$orders = new SplQueue();

$orders->enqueue("Order A");
$orders->enqueue("Order B");
$orders->enqueue("Order C");

while (!$orders->isEmpty()) {
    echo $orders->dequeue() . "\n";
}
```

---

## SplStack (LIFO)

```php
$navigation = new SplStack();

$navigation->push("Home");
$navigation->push("Products");
$navigation->push("Checkout");

while (!$navigation->isEmpty()) {
    echo $navigation->pop() . "\n";
}
```

---

## SplPriorityQueue

```php
$tickets = new SplPriorityQueue();

$tickets->insert("Low Priority", 1);
$tickets->insert("High Priority", 3);
$tickets->insert("Medium Priority", 2);

while ($tickets->valid()) {
    echo $tickets->current() . "\n";
    $tickets->next();
}
```

Output:

```text
High Priority
Medium Priority
Low Priority
```

---

# 7. Nested Loops

```php
$matrix = [
    [1, 2, 3],
    [4, 5, 6]
];

for ($row = 0; $row < count($matrix); $row++) {
    for ($col = 0; $col < count($matrix[$row]); $col++) {
        echo $matrix[$row][$col] . " ";
    }
    echo "\n";
}
```

---

# 8. Real-World Example

Combining control flow, match expressions, readonly classes, and associative arrays.

```php
readonly class User {
    public function __construct(
        public string $name,
        public int $age
    ) {}
}

$users = [
    new User("John", 25),
    new User("Jane", 17),
    new User("Bob", 32)
];

foreach ($users as $user) {
    $category = match (true) {
        $user->age >= 18 => "Adult",
        default => "Minor",
    };

    echo "{$user->name} - {$category}\n";
}
```

---

# Summary

## Control Flow

- `if` / `elseif` / `else`
- `switch` (loose comparison)
- `match` (strict type-safe lookup expression in PHP 8.0+)

## Loops

- `for`
- `foreach` (array iteration)
- `while`
- `do-while`

## Loop Controls

- `break` (supports nested escape)
- `continue` (supports nested skip)
- `return`

## Common Data Structures

| Data Structure         | PHP / SPL Equivalent         | Purpose                   |
| ---------------------- | ---------------------------- | ------------------------- |
| List / Array           | `array` (indexed)            | Ordered dynamic sequence  |
| Dictionary             | `array` (associative)        | Key-value mapping         |
| Queue                  | `SplQueue`                   | FIFO processing           |
| Stack                  | `SplStack`                   | LIFO processing           |
| Priority Queue         | `SplPriorityQueue`           | Priority-based processing |

Modern PHP promotes clean structural branching using **match expressions**, constructor property promotion, and SPL classes to optimize memory and execution flow.
