# 1. Creating Strings

## String Literal

```python
first_name = "John"
last_name = "Doe"
```

---

## Empty String

```python
empty = ""
```

---

## Multi-line & Raw String Literals

Raw string literals (`r"..."`) ignore escape sequences. Triple quotes (`"""..."""`) represent multi-line strings.

```python
json_text = """
{
    "name": "John",
    "age": 25
}
"""

print(json_text)
```

---

# 2. String Interpolation (f-strings)

The preferred, modern way to build strings in Python.

```python
name = "John"
age = 25

message = f"Name: {name}, Age: {age}"

print(message)
```

Output:

```text
Name: John, Age: 25
```

---

# 3. Concatenation

## Using `+`

```python
full_name = first_name + " " + last_name
```

---

## Using `join` (Efficient)

```python
full_name = " ".join([first_name, last_name])
```

---

# 4. Length

```python
name = "OpenAI"

print(len(name))
```

Output:

```text
6
```

---

# 5. Accessing Characters

```python
name = "OpenAI"

print(name[0])
print(name[1])
```

Output:

```text
O
p
```

---

# 6. Contains

## Case-Sensitive Checks

```python
email = "john@example.com"

has_domain = "@" in email

print(has_domain)
```

Output:

```text
True
```

---

## Case-Insensitive Checks

```python
text = "Hello World"

exists = "world" in text.lower()
```

---

# 7. StartsWith

```python
url = "https://openai.com"

print(url.startswith("https"))
```

---

# 8. EndsWith

```python
file = "report.pdf"

print(file.endswith(".pdf"))
```

---

# 9. IndexOf (find / index)

Finds the position of a substring. `find()` returns `-1` if not found, while `index()` raises `ValueError`.

```python
text = "Hello World"

position = text.find("World")

print(position)
```

Output:

```text
6
```

---

# 10. Replace

```python
text = "Hello World"

result = text.replace("World", "Python")

print(result)
```

Output:

```text
Hello Python
```

---

# 11. ToUpper

```python
text = "hello"

print(text.upper())
```

Output:

```text
HELLO
```

---

# 12. ToLower

```python
text = "HELLO"

print(text.lower())
```

Output:

```text
hello
```

---

# 13. Trim (strip)

Removes leading and trailing whitespace.

```python
text = "   Hello World   "

print(text.strip())
```

Output:

```text
Hello World
```

## lstrip / rstrip

```python
text.lstrip()  # leading whitespace
text.rstrip()  # trailing whitespace
```

---

# 14. Split

Convert a string into multiple parts based on a separator.

```python
csv = "John,Jane,Bob"

names = csv.split(",")

for name in names:
    print(name)
```

---

# 15. Join

Combine a list of strings using a delimiter.

```python
names = ["John", "Jane", "Bob"]

result = ", ".join(names)

print(result)
```

Output:

```text
John, Jane, Bob
```

---

# 16. Substring (Slicing)

Extract a portion of a string.

```python
text = "Hello World"

value = text[6:]

print(value)
```

Output:

```text
World
```

---

# 17. Remove (Slicing equivalent)

```python
text = "Hello World"

# Remove up to index 5
result = text[:5]

print(result)
```

Output:

```text
Hello
```

---

# 18. Insert

Strings are immutable, so insert operations are done via slicing.

```python
text = "Hello"

result = text[:5] + " World" + text[5:]

print(result)
```

Output:

```text
Hello World
```

---

# 19. String Comparison

## Equality

```python
a = "John"
b = "John"

print(a == b)
```

Output:

```text
True
```

---

## Case-Insensitive Comparison

```python
same = "john".lower() == "JOHN".lower()

print(same)
```

Output:

```text
True
```

---

# 20. StringIO (Analogous to StringBuilder)

For frequent string modifications, use `io.StringIO` to prevent creating excessive intermediate string objects.

```python
import io

builder = io.StringIO()

builder.write("Hello")
builder.write(" ")
builder.write("World")

print(builder.getvalue())
```

Output:

```text
Hello World
```

---

# 21. Null or Empty Checks

In Python, `None` and empty strings `""` are falsy, which simplifies checking.

```python
value = ""

if not value:
    print("String is None or empty")
```

---

# 22. Null, Empty, or Whitespace Check

```python
value = " "

if not value or value.isspace():
    print("String is None, empty, or whitespace")
```

---

# 23. String Enumeration (Characters)

```python
word = "Python"

for character in word:
    print(character)
```

---

# 24. Real-World Example

Generating a username.

```python
first_name = "John"
last_name = "Doe"

username = f"{first_name}.{last_name}".lower().replace(" ", "")

print(username)
```

Output:

```text
john.doe
```

---

# 25. Modern String Formatting (f-string Formatting Specifiers)

```python
name = "John"
salary = 50000.0

# Format as currency
print(f"{name} earns ${salary:,.2f}")
```

Output:

```text
John earns $50,000.00
```

---

# Summary

## Creation

- String literals
- Multi-line literals (`"""`)
- f-strings (`f"..."`)

## Searching

- `in` operator
- `startswith()`
- `endswith()`
- `find()` / `index()`

## Modification

- `replace()`
- `strip()` / `lstrip()` / `rstrip()`
- Slicing (`[start:end]`)

## Comparison

- `==`
- `.lower()` comparisons

## Utilities

- `split()`
- `join()`
- `len()`
- `io.StringIO` for building strings
- `isspace()` checks

Strings are immutable in Python. Every modification creates a new string object. For large-scale loop concatenations, append to a list and use `" ".join(list)`, or use `io.StringIO`.
