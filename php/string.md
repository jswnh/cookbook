# 1. Creating Strings

## Single Quotes vs Double Quotes

Single quotes (`'`) treat strings as literals. Double quotes (`"`) support variable parsing and escape sequences.

```php
$name = "John";
echo 'Hello $name\n'; // Output: Hello $name\n
echo "Hello {$name}\n"; // Output: Hello John (followed by newline)
```

---

## Heredoc and Nowdoc (Multi-line Strings)

Heredoc (double quotes behavior) and Nowdoc (single quotes behavior) are used for multi-line text blocks.

```php
// Heredoc
$json = <<<JSON
{
    "name": "{$name}",
    "age": 25
}
JSON;
```

---

# 2. String Interpolation

Variable parsing inside double quotes. Curly braces `{}` prevent ambiguity.

```php
$name = "John";
$age = 25;

$message = "Name: {$name}, Age: {$age}";
```

---

# 3. Concatenation

PHP uses the dot operator (`.`) for string concatenation.

```php
$fullName = $firstName . " " . $lastName;
```

---

# 4. Length

Use `strlen()` for byte length. For UTF-8 multi-byte character strings, use `mb_strlen()`.

```php
$name = "OpenAI";

echo strlen($name) . "\n";
```

Output:

```text
6
```

---

# 5. Accessing Characters

Strings can be accessed using zero-based array offsets.

```php
$name = "OpenAI";

echo $name[0] . "\n";
echo $name[1] . "\n";
```

Output:

```text
O
p
```

---

# 6. Contains

## Case-Sensitive Contains (PHP 8.0+)

```php
$email = "john@example.com";

$hasDomain = str_contains($email, "@");

var_dump($hasDomain);
```

Output:

```text
bool(true)
```

---

## Case-Insensitive Contains

```php
$text = "Hello World";

$exists = str_contains(strtolower($text), "world");
```

---

# 7. StartsWith (PHP 8.0+)

```php
$url = "https://openai.com";

echo str_starts_with($url, "https") ? "Yes" : "No";
```

---

# 8. EndsWith (PHP 8.0+)

```php
$file = "report.pdf";

echo str_ends_with($file, ".pdf") ? "Yes" : "No";
```

---

# 9. IndexOf (strpos)

Finds the position of a substring. Returns `false` if not found. Always use strict comparison (`===`) because the match could be at index `0`.

```php
$text = "Hello World";

$position = strpos($text, "World");

if ($position !== false) {
    echo "Found at: {$position}\n";
}
```

Output:

```text
Found at: 6
```

---

# 10. Replace

`str_replace()` is case-sensitive. Use `str_ireplace()` for case-insensitive replacements.

```php
$text = "Hello World";

$result = str_replace("World", "PHP", $text);

echo $result . "\n";
```

Output:

```text
Hello PHP
```

---

# 11. ToUpper

```php
$text = "hello";

echo strtoupper($text) . "\n";
```

Output:

```text
HELLO
```

*Note: For UTF-8, use `mb_strtoupper()`.*

---

# 12. ToLower

```php
$text = "HELLO";

echo strtolower($text) . "\n";
```

Output:

```text
hello
```

*Note: For UTF-8, use `mb_strtolower()`.*

---

# 13. Trim

Removes leading and trailing whitespace.

```php
$text = "   Hello World   ";

echo trim($text) . "\n";
```

Output:

```text
Hello World
```

## ltrim / rtrim

```php
ltrim($text); // removes leading whitespace
rtrim($text); // removes trailing whitespace
```

---

# 14. Split (explode)

Convert a string into an array based on a separator.

```php
$csv = "John,Jane,Bob";

$names = explode(",", $csv);

foreach ($names as $name) {
    echo $name . "\n";
}
```

---

# 15. Join (implode)

Combine an array of strings into a single string.

```php
$names = ["John", "Jane", "Bob"];

$result = implode(", ", $names);

echo $result . "\n";
```

Output:

```text
John, Jane, Bob
```

---

# 16. Substring (substr)

Extract a portion of a string.

```php
$text = "Hello World";

$value = substr($text, 6);

echo $value . "\n";
```

Output:

```text
World
```

*Note: For UTF-8, use `mb_substr()`.*

---

# 17. Remove

```php
$text = "Hello World";

// Remove " World"
$result = str_replace(" World", "", $text);
```

---

# 18. Insert

Use `substr_replace()` to insert a string at a specific position.

```php
$text = "Hello";

$result = substr_replace($text, " World", 5, 0);

echo $result . "\n";
```

Output:

```text
Hello World
```

---

# 19. String Comparison

## Strict Equality

```php
$a = "John";
$b = "John";

echo ($a === $b) ? "Equal" : "Not Equal";
```

---

## Case-Insensitive Comparison

Use `strcasecmp()` which returns `0` if the strings are equal.

```php
$same = (strcasecmp("john", "JOHN") === 0);
```

---

# 20. StringBuilder (Array buffer)

PHP strings are mutable but grow dynamically. For building large string sequences in loops, appending to an array and using `implode()` is highly memory-efficient.

```php
$buffer = [];

$buffer[] = "Hello";
$buffer[] = " ";
$buffer[] = "World";

echo implode("", $buffer) . "\n";
```

Output:

```text
Hello World
```

---

# 21. Null or Empty Checks

Use `empty()` or strict checks. `empty()` returns true for `null`, `false`, `0`, `""`, and `[]`.

```php
$value = "";

if (empty($value)) {
    echo "Empty or null\n";
}
```

---

# 22. Null, Empty, or Whitespace Check

```php
$value = " ";

if ($value === null || trim($value) === "") {
    echo "String is null, empty, or whitespace\n";
}
```

---

# 23. String Enumeration (Characters)

Convert a string to an array of characters using `str_split()` or `mb_str_split()`.

```php
$word = "PHP";

foreach (str_split($word) as $char) {
    echo $char . "\n";
}
```

---

# 24. Real-World Example

Generating a username.

```php
$firstName = "John";
$lastName = "Doe";

$username = str_replace(" ", "", strtolower($firstName . "." . $lastName));

echo $username . "\n";
```

Output:

```text
john.doe
```

---

# 25. Formatting (sprintf)

```php
$name = "John";
$salary = 50000;

echo sprintf("%s earns $%.2f\n", $name, $salary);
```

Output:

```text
John earns $50000.00
```

---

# Summary

## Creation

- Double Quotes (interpolation) vs Single Quotes (literal)
- Heredoc (`<<<TAG`) and Nowdoc (`<<<'TAG'`)

## Searching

- `str_contains()` (PHP 8.0+)
- `str_starts_with()` / `str_ends_with()` (PHP 8.0+)
- `strpos()` (strict comparison `!== false`)

## Modification

- `str_replace()` / `str_ireplace()`
- `trim()` / `ltrim()` / `rtrim()`
- `substr()` / `substr_replace()`

## Comparison

- `===` (identity check)
- `strcasecmp()` (case-insensitive check)

## Utilities

- `explode()` (split) and `implode()` (join)
- `strlen()` and `mb_strlen()` (UTF-8 character count)
- Array buffering for string builders

PHP strings are dynamic and binary-safe. Always use `mb_*` (Multibyte String) functions when dealing with UTF-8 data to ensure correct character calculations.
