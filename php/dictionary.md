# 1. Creating a Dictionary (Associative Array)

PHP uses associative arrays as dictionaries where keys can be integers or strings.

```php
$users = [
    1 => "John",
    2 => "Jane",
    3 => "Bob"
];
```

---

# 2. Adding Items

## Key Assignment

```php
$users = [];

$users[1] = "John";
$users[2] = "Jane";
```

---

# 3. Accessing Values

```php
$users = [
    1 => "John",
    2 => "Jane"
];

echo $users[1] . "\n";
```

Output:

```text
John
```

---

# 4. Updating Values

```php
$users = [
    1 => "John"
];

$users[1] = "Johnny";

echo $users[1] . "\n";
```

Output:

```text
Johnny
```

---

# 5. Dictionary Count

```php
$users = [
    1 => "John",
    2 => "Jane",
    3 => "Bob"
];

echo count($users) . "\n";
```

Output:

```text
3
```

---

# 6. Checking for Keys & Values

## Checking for Keys

`array_key_exists()` checks if a key exists even if its value is `null`. `isset()` checks if the key exists and its value is not `null` (faster).

```php
$users = [1 => "John", 2 => null];

if (array_key_exists(2, $users)) {
    echo "Key 2 exists (even though it's null)\n";
}

if (isset($users[1])) {
    echo "User 1 exists and is not null\n";
}
```

---

## Checking for Values

```php
if (in_array("John", $users, true)) {
    echo "Found\n";
}
```

---

# 7. Safe Retrieval

Use the null coalescing operator (`??`) to retrieve a value safely without raising an `Undefined array key` warning.

```php
$users = [
    1 => "John",
    2 => "Jane"
};

$name = $users[1] ?? "Default Name";
$notFound = $users[99] ?? "Default Name";

echo $name . "\n";
echo $notFound . "\n";
```

Output:

```text
John
Default Name
```

---

# 8. Removing Items

Use `unset()` to remove a key-value pair.

```php
$users = [
    1 => "John",
    2 => "Jane"
];

unset($users[1]);
```

---

# 9. Clearing a Dictionary

```php
$users = [];
```

---

# 10. Iterating Through a Dictionary

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

Output:

```text
1: John
2: Jane
3: Bob
```

---

# 11. Iterating Keys

```php
foreach (array_keys($users) as $id) {
    echo $id . "\n";
}
```

---

# 12. Iterating Values

```php
foreach (array_values($users) as $name) {
    echo $name . "\n";
}
```

---

# 13. Nested Dictionaries

```php
$departments = [
    "IT" => [
        "Manager" => "John",
        "Developer" => "Jane"
    ],
    "HR" => [
        "Manager" => "Bob"
    ]
];

echo $departments["IT"]["Developer"] . "\n";
```

Output:

```text
Jane
```

---

# 14. Dictionary with Classes (Value Objects)

```php
readonly class User {
    public function __construct(
        public int $id,
        public string $name,
        public string $email
    ) {}
}

$users = [
    1 => new User(1, "John", "john@example.com"),
    2 => new User(2, "Jane", "jane@example.com")
];

echo $users[1]->name . "\n";
```

Output:

```text
John
```

---

# 15. Functional Filtering and Mapping

## Filtering

```php
$users = [1 => "John", 2 => "Jane", 3 => "Bob"];

// Filter keys/values (use ARRAY_FILTER_USE_BOTH flag)
$admins = array_filter($users, fn($id) => $id > 1, ARRAY_FILTER_USE_KEY);

foreach ($admins as $id => $name) {
    echo "{$id}: {$name}\n";
}
```

---

## Selecting Values

```php
$names = array_values($users);
```

---

# 16. Converting a List to a Dictionary

Using `array_combine()` or reducing.

```php
$usersList = [
    new User(1, "John", "john@example.com"),
    new User(2, "Jane", "jane@example.com")
];

$userDictionary = [];
foreach ($usersList as $user) {
    $userDictionary[$user->id] = $user;
}
```

---

# 17. Merging Dictionaries

Use `+` operator to keep the keys of the first array (does not overwrite), or `array_merge()` / spread operator (`...`) to overwrite duplicates.

```php
$first = [1 => "John", 2 => "Jane"];
$second = [2 => "Overwritten", 3 => "Bob"];

// Union Operator (preserves original values for duplicates)
$union = $first + $second;

// Merged Array (overwrites numeric or string keys accordingly)
// PHP 8.1+ supports unpacking string keys
$merged = [...$first, ...$second];
```

---

# 18. Real-World Example

Caching users by ID.

```php
readonly class User {
    public function __construct(public int $id, public string $name) {}
}

$cache = [];

$cache[1] = new User(1, "John");
$cache[2] = new User(2, "Jane");

$user = $cache[1] ?? null;

if ($user !== null) {
    echo $user->name . "\n";
}
```

Output:

```text
John
```

---

# 19. Dictionary Performance

| Operation   | Average Complexity |
| ----------- | ------------------ |
| Add         | O(1)               |
| Update      | O(1)               |
| Remove      | O(1)               |
| Lookup      | O(1)               |
| Key Check   | O(1)               |

PHP associative arrays are highly optimized hash tables that maintain insertion order.

---

# Summary

## Core Operations

```php
$users[1] = "John";
unset($users[1]);
isset($users[1]);
array_key_exists(1, $users);
$value = $users[1] ?? null;
```

## Iteration

```php
foreach ($users as $key => $value) {
    echo "{$key}: {$value}\n";
}
```

## Modern Features

- Null Coalescing Operator (`??`)
- Arrow functions (`fn() => ...`)
- Spread operator unpacking (`[...$arr1, ...$arr2]`)
- Readonly classes for structured dictionary values

PHP associative arrays are standard dictionary equivalents. They provide fast key-based lookups and support both string and numeric keys natively.
