# 1. Conditional Statements

## if

Execute code when a condition is true.

```python
age = 18

if age >= 18:
    print("Adult")
```

---

## if-else

```python
age = 16

if age >= 18:
    print("Adult")
else:
    print("Minor")
```

---

## if-elif-else

```python
score = 85

if score >= 90:
    print("A")
elif score >= 80:
    print("B")
elif score >= 70:
    print("C")
else:
    print("Failed")
```

---

## Structural Pattern Matching (match-case)

Introduced in Python 3.10+, this is Python's equivalent to the switch statement.

```python
role = "Admin"

match role:
    case "Admin":
        print("Full Access")
    case "Manager":
        print("Manage Resources")
    case "User":
        print("Limited Access")
    case _:
        print("Unknown Role")
```

### Multiple Cases

```python
from datetime import date

day = date.today().strftime("%A")

match day:
    case "Saturday" | "Sunday":
        print("Weekend")
    case _:
        print("Weekday")
```

### Pattern Matching with Types and Guards

```python
value = 150

match value:
    case int(number) if number > 100:
        print("Large Number")
    case int():
        print("Small Number")
    case str():
        print("Text")
    case None:
        print("Null")
    case _:
        print("Unknown")
```

---

# 2. Loops

## for with range()

Best when the iteration count is known.

```python
for i in range(1, 6):
    print(i)
```

---

## for-in (Iterating Collections)

Preferred for iterating collections.

```python
languages = ["C#", "TypeScript", "Python"]

for language in languages:
    print(language)
```

---

## while

```python
count = 1

while count <= 5:
    print(count)
    count += 1
```

---

## do-while (Workaround)

Python does not have a native `do-while` loop. You can implement it using a `while True` loop with a conditional `break` at the end.

```python
count = 1

while True:
    print(count)
    count += 1
    if count > 5:
        break
```

---

## async for

Used with asynchronous generators.

```python
async for number in get_numbers_async():
    print(number)
```

---

# 3. Loop Control Statements

## break

Stops execution of a loop.

```python
for i in range(1, 11):
    if i == 5:
        break
    print(i)
```

---

## continue

Skips the current iteration.

```python
for i in range(1, 6):
    if i == 3:
        continue
    print(i)
```

---

# 4. Lists

Dynamic, ordered sequence of elements.

```python
numbers = [10, 20, 30, 40, 50]

for number in numbers:
    print(number)
```

---

# 5. Dictionaries (dict)

Key-value storage.

```python
users = {
    1: "John",
    2: "Jane",
    3: "Bob"
}

for user_id, name in users.items():
    print(f"{user_id}: {name}")
```

---

# 6. Sets (set)

Stores unique, unordered values.

```python
skills = {"Python", "SQL", "Python", "TypeScript"}

for skill in skills:
    print(skill)
```

Output:
```text
Python
SQL
TypeScript
```

---

# 7. Queues (collections.deque)

FIFO (First In, First Out) queue using double-ended queue.

```python
from collections import deque

orders = deque()

orders.append("Order A")
orders.append("Order B")
orders.append("Order C")

while orders:
    print(orders.popleft())
```

---

# 8. Stacks (list)

LIFO (Last In, First Out) stack using list methods.

```python
navigation = []

navigation.append("Home")
navigation.append("Products")
navigation.append("Checkout")

while navigation:
    print(navigation.pop())
```

---

# 9. PriorityQueue (heapq)

Priority-based processing using heapq module (min-heap).

```python
import heapq

tickets = []

# (priority, value)
heapq.heappush(tickets, (3, "Low Priority"))
heapq.heappush(tickets, (2, "Medium Priority"))
heapq.heappush(tickets, (1, "High Priority"))

while tickets:
    priority, item = heapq.heappop(tickets)
    print(item)
```

---

# 10. Nested Loops

Useful for matrices and multidimensional data.

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6]
]

for row in matrix:
    for value in row:
        print(value, end=" ")
    print()
```

---

# 11. Real-World Example

Combining control flow, loops, classes, and collections.

```python
from dataclasses import dataclass

@dataclass
class User:
    name: str
    age: int

users = [
    User("John", 25),
    User("Jane", 17),
    User("Bob", 32)
]

for user in users:
    category = "Adult" if user.age >= 18 else "Minor"
    print(f"{user.name} - {category}")
```

---

# Summary

## Control Flow

- if
- if-else
- match-case (structural pattern matching)
- conditional expressions (ternary operators)

## Loops

- for in range()
- for-in
- while
- async for

## Loop Controls

- break
- continue
- return

## Common Data Structures

| Data Structure         | Python Equivalent            | Purpose                   |
| ---------------------- | ---------------------------- | ------------------------- |
| List                   | `list`                       | Dynamic collection        |
| Dictionary             | `dict`                       | Key-value storage         |
| Set                    | `set`                        | Unique values             |
| Queue                  | `collections.deque`          | FIFO processing           |
| Stack                  | `list`                       | LIFO processing           |
| Priority Queue         | `heapq` / `PriorityQueue`    | Priority-based processing |

Modern Python encourages pattern matching (`match-case`), list comprehensions, and generator expressions to write cleaner and more readable code.
