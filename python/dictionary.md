# 1. Creating a Dictionary

```python
users = {
    1: "John",
    2: "Jane",
    3: "Bob"
}
```

---

# 2. Adding Items

## Using Indexer / Key Assignment

```python
users = {}

users[1] = "John"
users[2] = "Jane"
```

---

## Using `update()`

```python
users = {}

users.update({1: "John", 2: "Jane"})
```

---

# 3. Accessing Values

```python
users = {
    1: "John",
    2: "Jane"
}

print(users[1])
```

Output:

```text
John
```

---

# 4. Updating Values

```python
users = {
    1: "John"
}

users[1] = "Johnny"

print(users[1])
```

Output:

```text
Johnny
```

---

# 5. Dictionary Count (Length)

```python
users = {
    1: "John",
    2: "Jane",
    3: "Bob"
}

print(len(users))
```

Output:

```text
3
```

---

# 6. Checking for Keys & Values

## Checking for Keys (using `in`)

```python
if 1 in users:
    print("User exists")
```

---

## Checking for Values

```python
if "John" in users.values():
    print("Found")
```

---

# 7. Safe Retrieval with `get()`

Preferred over direct key lookups when a key may not exist to avoid `KeyError`.

```python
users = {
    1: "John",
    2: "Jane"
}

# Returns None if not found, or optional default second parameter
name = users.get(1)
not_found = users.get(99, "Default Name")

print(name)
print(not_found)
```

Output:

```text
John
Default Name
```

---

# 8. Removing Items

## Using `del`

```python
users = {
    1: "John",
    2: "Jane"
}

del users[1]
```

---

## Using `pop()`

Removes the key and returns its value.

```python
users = {
    1: "John",
    2: "Jane"
}

name = users.pop(1)
```

---

# 9. Clearing a Dictionary

```python
users.clear()
```

---

# 10. Iterating Through a Dictionary

## Using `items()`

```python
for user_id, name in users.items():
    print(f"{user_id}: {name}")
```

Output:

```text
1: John
2: Jane
3: Bob
```

---

# 11. Iterating Keys

```python
for user_id in users.keys():
    print(user_id)
```

Or directly (default iteration over dict is over keys):

```python
for user_id in users:
    print(user_id)
```

---

# 12. Iterating Values

```python
for name in users.values():
    print(name)
```

---

# 13. Nested Dictionaries

```python
departments = {
    "IT": {
        "Manager": "John",
        "Developer": "Jane"
    },
    "HR": {
        "Manager": "Bob"
    }
}

print(departments["IT"]["Developer"])
```

Output:

```text
Jane
```

---

# 14. Dictionary with Dataclasses

Modern Python uses dataclasses to store structured records as values.

```python
from dataclasses import dataclass

@dataclass
class User:
    id: int
    name: str
    email: str

users = {
    1: User(1, "John", "john@example.com"),
    2: User(2, "Jane", "jane@example.com")
}

print(users[1].name)
```

Output:

```text
John
```

---

# 15. Dictionary Comprehensions

Python's modern equivalent of functional projection.

```python
square_dict = {num: num*num for num in range(1, 4)}

print(square_dict)
```

Output:

```text
{1: 1, 2: 4, 3: 9}
```

---

# 16. Filtering and Mapping (LINQ Analogs)

## Filtering

```python
users = {1: "John", 2: "Jane", 3: "Bob"}

admins = {user_id: name for user_id, name in users.items() if user_id > 1}

for user_id, name in admins.items():
    print(f"{user_id}: {name}")
```

---

## Selecting Values

```python
names = [name for name in users.values()]
```

---

# 17. Converting a List to a Dictionary

```python
users_list = [
    User(1, "John", "john@example.com"),
    User(2, "Jane", "jane@example.com")
]

user_dict = {user.id: user for user in users_list}
```

---

# 18. Merging Dictionaries

Using dictionary unpacking operator (`**`) or union operator (`|` in Python 3.9+).

```python
first = {1: "John", 2: "Jane"}
second = {3: "Bob", 4: "Alice"}

# Modern Union Operator (Python 3.9+)
merged = first | second

# Or unpacking
merged_alternative = {**first, **second}
```

---

# 19. Real-World Example

Caching users by ID.

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class User:
    id: int
    name: str

cache: dict[int, User] = {}

cache[1] = User(1, "John")
cache[2] = User(2, "Jane")

user = cache.get(1)
if user is not None:
    print(user.name)
```

Output:

```text
John
```

---

# 20. Dictionary Performance

| Operation   | Average Complexity |
| ----------- | ------------------ |
| Add / Set   | O(1)               |
| Update      | O(1)               |
| Remove      | O(1)               |
| Lookup      | O(1)               |
| Key Check   | O(1)               |

Dictionaries are highly optimized hash tables in Python, and since Python 3.7 they natively preserve insertion order.

---

# Common Use Cases

- User lookup by ID
- In-memory caching
- Configuration settings
- API response serialization / JSON mapping
- Frequency counters (using `collections.Counter`)
- In-memory indexes

---

# Summary

## Core Operations

```python
users[1] = "John"
users[1] = "Jane"
del users[1]
1 in users
value = users.get(1)
```

## Iteration

```python
for key, value in users.items():
    print(f"{key}: {value}")
```

## Modern Python Features

- Dict Union Operator (`|`)
- Dictionary Comprehensions
- Dataclasses
- Type Annotations (`dict[int, User]`)
- Safe recovery with `.get(key, default)`

`dict` is one of the most fundamental and highly-optimized data structures in Python, providing extremely fast key-based access and serving as the foundation of namespace management in the language itself.
