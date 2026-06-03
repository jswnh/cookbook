# 1. Creating Lists

## Traditional Syntax

```python
numbers = [10, 20, 30, 40, 50]
```

---

## Empty List

```python
numbers = []
```

---

## List with Fixed Size (Pre-filled)

```python
numbers = [0] * 5
```

Default values:

```text
0
0
0
0
0
```

---

# 2. Accessing Elements

Lists use zero-based indexing.

```python
fruits = ["Apple", "Banana", "Orange"]

print(fruits[0])
print(fruits[1])
```

Output:

```text
Apple
Banana
```

---

# 3. Modifying Elements

```python
fruits = ["Apple", "Banana", "Orange"]

fruits[1] = "Mango"

print(fruits[1])
```

Output:

```text
Mango
```

---

# 4. List Length

```python
numbers = [10, 20, 30, 40]

print(len(numbers))
```

Output:

```text
4
```

---

# 5. Iterating with for (index)

Best when indexes are needed.

```python
numbers = [10, 20, 30, 40]

for i in range(len(numbers)):
    print(f"Index: {i}, Value: {numbers[i]}")
```

---

# 6. Iterating with for-in

Preferred when indexes are not needed.

```python
numbers = [10, 20, 30, 40]

for number in numbers:
    print(number)
```

---

# 7. Multi-Dimensional Lists

Useful for matrices.

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6]
]

print(matrix[0][0])
print(matrix[1][2])
```

Output:

```text
1
6
```

---

## Iterating a Multi-Dimensional List

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

# 8. Jagged Lists (List of Lists)

An list of lists with varying sizes.

```python
matrix = [
    [1, 2, 3],
    [4, 5],
    [6, 7, 8, 9]
]
```

Accessing values:

```python
print(matrix[0][1])
print(matrix[2][3])
```

Output:

```text
2
9
```

---

# 9. Searching Lists

## Using `in`

```python
numbers = [10, 20, 30, 40]

exists = 30 in numbers

print(exists)
```

Output:

```text
True
```

---

## list.index()

```python
numbers = [10, 20, 30, 40]

index = numbers.index(30)

print(index)
```

Output:

```text
2
```

---

# 10. Sorting Lists

```python
numbers = [50, 20, 10, 40, 30]

numbers.sort()

for number in numbers:
    print(number)
```

Output:

```text
10
20
30
40
50
```

---

## Sorted (non-mutating)

```python
numbers = [50, 20, 10, 40, 30]

sorted_numbers = sorted(numbers)
```

---

# 11. Reversing Lists

```python
numbers = [10, 20, 30, 40, 50]

numbers.reverse()

for number in numbers:
    print(number)
```

Output:

```text
50
40
30
20
10
```

---

# 12. Copying Lists

```python
source = [10, 20, 30]

destination = source.copy()
```

Or using slicing:

```python
destination = source[:]
```

---

# 13. List Slicing

```python
numbers = [10, 20, 30, 40, 50]

result = numbers[1:4]

for number in result:
    print(number)
```

Output:

```text
20
30
40
```

---

# 14. Negative Indexing

Access elements from the end.

```python
numbers = [10, 20, 30, 40, 50]

print(numbers[-1])
print(numbers[-2])
```

Output:

```text
50
40
```

---

# 15. List Unpacking (Destructuring)

```python
names = ["John", "Jane", "Bob"]

first, second, *rest = names

print(first)
print(second)
```

---

# 16. Combining Lists

Using the `+` operator or unpacking.

```python
first = [1, 2, 3]
second = [4, 5, 6]

combined = first + second

for number in combined:
    print(number)
```

Output:

```text
1
2
3
4
5
6
```

---

# 17. List Comprehensions (Functional-Style Filtering)

```python
numbers = [10, 20, 30, 40, 50]

even_numbers = [n for n in numbers if n % 2 == 0]

for number in even_numbers:
    print(number)
```

---

# 18. Real-World Example

Processing student grades.

```python
grades = [95, 82, 76, 91, 88]

highest = max(grades)
lowest = min(grades)
average = sum(grades) / len(grades)

print(f"Highest: {highest}")
print(f"Lowest: {lowest}")
print(f"Average: {average:.2f}")
```

Output:

```text
Highest: 95
Lowest: 76
Average: 86.40
```

---

# 19. memoryview (Advanced)

High-performance view over contiguous memory (analogous to `Span<T>`).

```python
numbers = bytearray([10, 20, 30, 40, 50])

view = memoryview(numbers)

view[0] = 99

print(numbers[0])
```

Output:

```text
99
```

Useful for performance-critical applications because it avoids copying.

---

# Summary

## Creating Lists

```python
numbers = [1, 2, 3]
```

## Accessing Elements

```python
numbers[0]
numbers[-1]
```

## Iteration

- for with `range(len(...))`
- for-in

## List Operations

- `sort()` / `sorted()`
- `reverse()`
- `copy()`
- `in` operator
- `index()`

## Modern Features

- List comprehensions (`[x for x in ...]`)
- Unpacking (`*rest`)
- Slicing (`[1:4]`)
- Negative indexing (`[-1]`)
- `memoryview`

Lists are dynamic, flexible collections and serve as the foundation for most data manipulation in Python.
