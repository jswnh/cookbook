## 1. Value Types

Value types store the actual data directly in memory (typically on the stack).

### Integral Types (Whole Numbers)

| Keyword      | .NET Type        | Size         | Range                                                   | Example                                    |
| :----------- | :--------------- | :----------- | :------------------------------------------------------ | :----------------------------------------- |
| **`sbyte`**  | `System.SByte`   | 8-bit        | -128 to 127                                             | `sbyte temperature = -15;`                 |
| **`byte`**   | `System.Byte`    | 8-bit        | 0 to 255                                                | `byte age = 25;`                           |
| **`short`**  | `System.Int16`   | 16-bit       | -32,768 to 32,767                                       | `short elevation = -500;`                  |
| **`ushort`** | `System.UInt16`  | 16-bit       | 0 to 65,535                                             | `ushort port = 8080;`                      |
| **`int`**    | `System.Int32`   | 32-bit       | -2,147,483,648 to 2,147,483,647                         | `int population = 1500000;`                |
| **`uint`**   | `System.UInt32`  | 32-bit       | 0 to 4,294,967,295                                      | `uint distance = 3000000U;`                |
| **`long`**   | `System.Int64`   | 64-bit       | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 | `long starsInGalaxy = 100000000000L;`      |
| **`ulong`**  | `System.UInt64`  | 64-bit       | 0 to 18,446,744,073,709,551,615                         | `ulong maxBytes = 18446744073709551615UL;` |
| **`nint`**   | `System.IntPtr`  | OS Dependent | Native-sized signed integer                             | `nint memoryAddress = 0x00000001;`         |
| **`nuint`**  | `System.UIntPtr` | OS Dependent | Native-sized unsigned integer                           | `nuint handle = 0x000000F4;`               |

### Floating-Point & Decimal Types (Fractional Numbers)

| Keyword       | .NET Type        | Size    | Best For                                             | Example                                    |
| :------------ | :--------------- | :------ | :--------------------------------------------------- | :----------------------------------------- |
| **`float`**   | `System.Single`  | 32-bit  | Single-precision floating point (Graphics, 3D math)  | `float pi = 3.14f;`                        |
| **`double`**  | `System.Double`  | 64-bit  | Double-precision floating point (Scientific calc)    | `double plancksConstant = 6.62607015e-34;` |
| **`decimal`** | `System.Decimal` | 128-bit | Financial and monetary calculations (high precision) | `decimal accountBalance = 1999.99m;`       |

### Other Built-in Value Types

| Keyword      | .NET Type          | Description                              | Example                                               |
| :----------- | :----------------- | :--------------------------------------- | :---------------------------------------------------- |
| **`char`**   | `System.Char`      | 16-bit single Unicode character          | `char grade = 'A';`                                   |
| **`bool`**   | `System.Boolean`   | 8-bit logical value (`true` or `false`)  | `bool isActive = true;`                               |
| **`struct`** | `System.ValueType` | User-defined, lightweight data structure | `public struct Point { public int X; public int Y; }` |
| **`enum`**   | `System.Enum`      | User-defined sets of named constants     | `public enum Status { Pending, Approved, Denied }`    |

---

## 2. Reference Types

Reference types store the memory address (reference) of the data on the heap.

| Keyword         | .NET Type         | Description                                               | Example                                                  |
| :-------------- | :---------------- | :-------------------------------------------------------- | :------------------------------------------------------- |
| **`string`**    | `System.String`   | A sequence of Unicode characters                          | `string message = "Hello, World!";`                      |
| **`object`**    | `System.Object`   | The ultimate base class for all C# data types             | `object data = new { Name = "Josuan" };`                 |
| **`dynamic`**   | `System.Object`   | Bypasses compile-time type checking, resolved at runtime  | `dynamic jsonResult = FetchData(); jsonResult.id = 1;`   |
| **`class`**     | (Custom)          | User-defined reference types (OOP building blocks)        | `public class User { public string Name { get; set; } }` |
| **`interface`** | (Custom)          | Defines a contract that classes or structs must implement | `public interface IRepository { void Save(); }`          |
| **`delegate`**  | `System.Delegate` | A type that represents references to methods              | `public delegate void LogHandler(string message);`       |

---

## 3. Pointer Types

Pointers store the direct memory address of another variable. They require an `unsafe` context.

| Keyword           | Description                                       | Example                                     |
| :---------------- | :------------------------------------------------ | :------------------------------------------ |
| **`*` (Pointer)** | Stores a memory address (Used in `unsafe` blocks) | `unsafe { int num = 10; int* ptr = &num; }` |
