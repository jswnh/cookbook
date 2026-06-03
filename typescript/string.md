# 1. Creating Strings

## String Literal

```typescript
const firstName: string = "John";
const lastName: string = "Doe";
```

---

## Empty String

```typescript
const empty: string = "";
```

---

## Multi-line & Template Literals

Template literals (backticks `` ` ``) support multi-line strings natively.

```typescript
const json: string = `
{
    "name": "John",
    "age": 25
}
`;

console.log(json);
```

---

# 2. String Interpolation

Preferred way to build strings.

```typescript
const name: string = "John";
const age: number = 25;

const message: string = `Name: ${name}, Age: ${age}`;

console.log(message);
```

Output:

```text
Name: John, Age: 25
```

---

# 3. Concatenation

## Using `+`

```typescript
const fullName: string = firstName + " " + lastName;
```

---

## Using `concat`

```typescript
const fullName: string = firstName.concat(" ", lastName);
```

---

# 4. Length

```typescript
const name: string = "OpenAI";

console.log(name.length);
```

Output:

```text
6
```

---

# 5. Accessing Characters

```typescript
const name: string = "OpenAI";

console.log(name[0]);
console.log(name.charAt(1));
```

Output:

```text
O
p
```

---

# 6. Contains

## Case-Sensitive (includes)

```typescript
const email: string = "john@example.com";

const hasDomain: boolean = email.includes("@");

console.log(hasDomain);
```

Output:

```text
true
```

---

## Case-Insensitive Contains

```typescript
const text: string = "Hello World";

const exists: boolean = text.toLowerCase().includes("world");
```

---

# 7. StartsWith

```typescript
const url: string = "https://openai.com";

console.log(url.startsWith("https"));
```

---

# 8. EndsWith

```typescript
const file: string = "report.pdf";

console.log(file.endsWith(".pdf"));
```

---

# 9. IndexOf

Finds the position of a substring. Returns `-1` if not found.

```typescript
const text: string = "Hello World";

const position: number = text.indexOf("World");

console.log(position);
```

Output:

```text
6
```

---

# 10. Replace / ReplaceAll

Note: `replace()` only replaces the first occurrence of a string unless a global regex is provided. Use `replaceAll()` (ES2021+) to replace all occurrences.

```typescript
const text: string = "Hello World";

const result: string = text.replace("World", "TypeScript");

console.log(result);
```

Output:

```text
Hello TypeScript
```

---

# 11. ToUpper

```typescript
const text: string = "hello";

console.log(text.toUpperCase());
```

Output:

```text
HELLO
```

---

# 12. ToLower

```typescript
const text: string = "HELLO";

console.log(text.toLowerCase());
```

Output:

```text
hello
```

---

# 13. Trim

Removes leading and trailing whitespace.

```typescript
const text: string = "   Hello World   ";

console.log(text.trim());
```

Output:

```text
Hello World
```

## trimStart / trimEnd

```typescript
text.trimStart();
text.trimEnd();
```

---

# 14. Split

Convert a string into multiple parts based on a separator.

```typescript
const csv: string = "John,Jane,Bob";

const names: string[] = csv.split(",");

names.forEach(name => console.log(name));
```

---

# 15. Join

Combine an array of strings.

```typescript
const names: string[] = ["John", "Jane", "Bob"];

const result: string = names.join(", ");

console.log(result);
```

Output:

```text
John, Jane, Bob
```

---

# 16. Substring (slice / substring)

Extract a portion of a string.

```typescript
const text: string = "Hello World";

const value: string = text.slice(6); // or text.substring(6)

console.log(value);
```

Output:

```text
World
```

---

# 17. Remove (Slicing)

```typescript
const text: string = "Hello World";

const result: string = text.slice(0, 5);

console.log(result);
```

Output:

```text
Hello
```

---

# 18. Insert

Insert a substring into an index using slice.

```typescript
const text: string = "Hello";

const result: string = text.slice(0, 5) + " World" + text.slice(5);

console.log(result);
```

Output:

```text
Hello World
```

---

# 19. String Comparison

## Equality

```typescript
const a: string = "John";
const b: string = "John";

console.log(a === b);
```

Output:

```text
true
```

---

## Case-Insensitive Comparison

```typescript
const same: boolean = "john".toLowerCase() === "JOHN".toLowerCase();

console.log(same);
```

Output:

```text
true
```

---

# 20. StringBuilder (Array join)

JavaScript doesn't have a built-in `StringBuilder` class. For performance-intensive string concatenations, push strings to an array and join them.

```typescript
const builder: string[] = [];

builder.push("Hello");
builder.push(" ");
builder.push("World");

console.log(builder.join(""));
```

Output:

```text
Hello World
```

---

# 21. Null or Empty Checks

JavaScript/TypeScript has falsy values, meaning `null`, `undefined`, and `""` are all checked together.

```typescript
const value: string | null | undefined = "";

if (!value) {
    console.log("String is null, undefined, or empty");
}
```

---

# 22. Null, Empty, or Whitespace Check

```typescript
const value: string | null | undefined = " ";

if (!value || value.trim().length === 0) {
    console.log("String is null, undefined, empty, or whitespace");
}
```

---

# 23. String Enumeration (Characters)

Iterate over individual characters.

```typescript
const word: string = "OpenAI";

for (const character of word) {
    console.log(character);
}
```

---

# 24. Real-World Example

Generating a username.

```typescript
const firstName: string = "John";
const lastName: string = "Doe";

const username: string = `${firstName}.${lastName}`
    .toLowerCase()
    .replace(/\s+/g, ""); // regex replace all whitespace

console.log(username);
```

Output:

```text
john.doe
```

---

# 25. Padding and Formatting

Pad strings to reach a target length (useful for formatting).

```typescript
const hours: string = "9".padStart(2, "0");
const minutes: string = "5".padStart(2, "0");

console.log(`${hours}:${minutes}`);
```

Output:

```text
09:05
```

---

# Summary

## Creation

- String literals
- Template literals (backticks `` ` ``)
- String interpolation (`${}`)

## Searching

- `includes()`
- `startsWith()`
- `endsWith()`
- `indexOf()`

## Modification

- `replace()` / `replaceAll()`
- `trim()` / `trimStart()` / `trimEnd()`
- `slice()` / `substring()`

## Comparison

- `===` (Strict equality)
- `.toLowerCase()` comparisons

## Utilities

- `split()`
- `join()`
- `.length`
- Array buffering (`push` + `join`) for builders

Strings are immutable in JavaScript and TypeScript. Every modification creates a new string reference. For large loops with heavy modifications, collect fragments in an array and run `.join("")` to minimize performance overhead.
