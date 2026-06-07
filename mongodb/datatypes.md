# MongoDB Data Types Reference (v7.0 / v8.0)

MongoDB stores records as BSON (Binary JSON) documents. BSON extends JSON to support additional data types such as `ObjectId`, 64-bit integers, exact decimals, and binary data.

---

## 1. BSON Data Types Overview

| Type | Type Number (Alias) | Description | Example (JS / Mongo Shell) |
| :--- | :--- | :--- | :--- |
| **`Double`** | `1` (`"double"`) | 64-bit floating point number. Default numeric type in JS shell. | `3.14` |
| **`String`** | `2` (`"string"`) | UTF-8 encoded string. | `"John Doe"` |
| **`Object`** | `3` (`"object"`) | Nested embedded document. | `{"city": "NYC"}` |
| **`Array`** | `4` (`"array"`) | Zero-indexed list of values or documents. | `["blue", "green"]` |
| **`Binary data`** | `5` (`"binData"`) | Arbitrary binary payload (e.g. files, UUIDs). | `BinData(0, "eHh4...")` |
| **`ObjectId`** | `7` (`"objectId"`) | 12-byte unique document identifier. | `ObjectId()` |
| **`Boolean`** | `8` (`"bool"`) | Logical value (`true` or `false`). | `true` |
| **`Date`** | `9` (`"date"`) | UTC datetime (64-bit integer milliseconds). | `ISODate("2026-06-07")` |
| **`Null`** | `10` (`"null"`) | Represents a null value or missing field. | `null` |
| **`Regular Expression`** | `11` (`"regex"`) | Search pattern for regex queries. | `/^admin/i` |
| **`Int32`** | `16` (`"int"`) | 32-bit signed integer. | `NumberInt(42)` |
| **`Int64`** | `18` (`"long"`) | 64-bit signed integer. | `NumberLong("9007199254")` |
| **`Decimal128`** | `19` (`"decimal"`) | 128-bit decimal floating point. **Required for monetary values.** | `NumberDecimal("19.99")` |

---

## 2. ObjectId Composition

Every MongoDB document requires a unique `_id` field, which defaults to a 12-byte `ObjectId`. It is generated automatically if omitted.

```text
+------------------------+-------------------+----------------------+
| 4-Byte Timestamp       | 5-Byte Random     | 3-Byte Increment     |
| (Seconds since epoch)  | (Unique per host) | (Counter starts rand)|
+------------------------+-------------------+----------------------+
|<-------------------------- 12 Bytes ---------------------------->|
```

### ObjectId Features
- **Temporal Sorting**: Since the first 4 bytes are a Unix timestamp, ObjectIds sort naturally chronologically.
- **Extract timestamp**: You can extract the creation time of a document directly from its `_id` without storing a separate `created_at` field.
```javascript
// Extract timestamp in Mongo Shell
ObjectId("60b64d1f8f7b2c001f3c3a9d").getTimestamp();
// Output: 2021-06-01T15:00:15.000Z
```

---

## 3. Numeric Types Handling

JavaScript's native numeric type is a double-precision float. In MongoDB shell (which runs on V8 JS), numbers default to **`Double`**. You must use explicit constructor wrappers for integers and exact decimals.

```javascript
// Inserting exact types in MongoDB
db.products.insertOne({
    name: "Coffee Beans",
    stock_count: NumberInt(120),       // 32-bit Integer
    total_sales: NumberLong("54000"),  // 64-bit Long Integer
    price: NumberDecimal("14.99")       // 128-bit Decimal (Exact)
});
```

---

## 4. Nested Documents and Arrays

BSON documents allow rich nesting of schemas, eliminating the need for relational joins in many use cases.

```javascript
db.users.insertOne({
    _id: ObjectId(),
    username: "dev_user",
    profile: {
        first_name: "Jane",
        last_name: "Smith",
        age: NumberInt(30) -- Nested Object
    },
    roles: ["developer", "admin"], -- Array of Strings
    addresses: [
        { type: "work", zip: "10001", active: true },
        { type: "home", zip: "90210", active: false } -- Array of Objects
    ]
});
```

---

## 5. Dates and Times

Dates are stored in UTC.

```javascript
// 1. Insert current date/time
db.logs.insertOne({ event: "login", timestamp: new Date() });

// 2. Insert specific ISO date string
db.logs.insertOne({ event: "backup", timestamp: ISODate("2026-06-07T08:30:00Z") });
```
> [!WARNING]
> Running `Date()` inside the shell returns a string representation (e.g. `"Sun Jun 07 2026..."`). Always use `new Date()` or `ISODate()` to insert actual UTC Date types.
