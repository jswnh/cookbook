# MySQL JSON Data Type & Functions Reference

MySQL provides a native `JSON` type that validates incoming data and stores it in an optimized binary format. 

---

## 1. Creating and Inserting JSON Data

### Table Definition
```sql
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    attributes JSON
);
```

### Inserting JSON Documents
Values can be passed as standard JSON strings. MySQL will reject the query if formatting is invalid.
```sql
INSERT INTO products (name, attributes) VALUES (
    'Smart Watch',
    '{"brand": "TechCorp", "specs": {"water_proof": true, "battery_hours": 48}, "colors": ["black", "silver"]}'
);

-- Or construct JSON dynamically using functions
INSERT INTO products (name, attributes) VALUES (
    'Wireless Earbuds',
    JSON_OBJECT('brand', 'AudioCo', 'specs', JSON_OBJECT('anc', true), 'colors', JSON_ARRAY('black', 'white'))
);
```

---

## 2. Extracting JSON Data

MySQL offers built-in operators and functions to fetch nested properties from JSON.

### Extraction Operators (`->` and `->>`)

- **`->` (JSON_EXTRACT)**: Returns the matching value, keeping quotes around string results.
- **`->>` (JSON_UNQUOTE + JSON_EXTRACT)**: Returns the matching value, stripping outer quotes from string results.

```sql
-- Query with quotes: returns "TechCorp"
SELECT name, attributes->'$.brand' AS brand FROM products;

-- Query without quotes: returns TechCorp
SELECT name, attributes->>'$.brand' AS brand FROM products;

-- Query nested objects or array items
SELECT 
    name,
    attributes->'$.specs.water_proof' AS is_waterproof,
    attributes->>'$.colors[0]' AS primary_color
FROM products;
```

### Extraction Functions
- **`JSON_EXTRACT(json_doc, path)`**: Standard extract function.
- **`JSON_KEYS(json_doc[, path])`**: Returns keys at a specific depth level.

```sql
-- Extract key arrays
SELECT JSON_KEYS(attributes) FROM products; -- Returns ["brand", "specs", "colors"]
```

---

## 3. Modifying JSON Documents

JSON values should be modified using specific JSON functions rather than standard string updates.

| Function | Behavior |
| :--- | :--- |
| **`JSON_SET(json_doc, path, val[, path, val])`** | Inserts new keys or replaces existing keys. |
| **`JSON_INSERT(json_doc, path, val[, path, val])`** | Inserts new keys only; existing keys are left unchanged. |
| **`JSON_REPLACE(json_doc, path, val[, path, val])`** | Replaces existing keys only; new keys are ignored. |
| **`JSON_REMOVE(json_doc, path)`** | Deletes keys or array items at the specified path. |

### Modification Examples
```sql
-- Update / Insert battery_hours and add model code
UPDATE products
SET attributes = JSON_SET(attributes, '$.specs.battery_hours', 50, '$.model', 'TC-500')
WHERE id = 1;

-- Add a new color to the array
UPDATE products
SET attributes = JSON_ARRAY_APPEND(attributes, '$.colors', 'gold')
WHERE id = 1;

-- Remove waterproof tag
UPDATE products
SET attributes = JSON_REMOVE(attributes, '$.specs.water_proof')
WHERE id = 1;
```

---

## 4. Searching and Validation

- **`JSON_VALID(value)`**: Returns 1 if valid JSON, otherwise 0.
- **`JSON_CONTAINS(target, candidate[, path])`**: Checks if target document contains candidate.
- **`JSON_CONTAINS_PATH(json_doc, 'one'/'all', path[, path])`**: Checks if paths exist.

```sql
-- Find products where specifications indicate waterproof is true
SELECT name 
FROM products 
WHERE JSON_CONTAINS(attributes, 'true', '$.specs.water_proof');

-- Find products that have 'black' listed in colors array
SELECT name 
FROM products 
WHERE JSON_CONTAINS(attributes, '"black"', '$.colors');

-- Verify if path exists
SELECT name 
FROM products 
WHERE JSON_CONTAINS_PATH(attributes, 'one', '$.specs.battery_hours');
```

---

## 5. JSON_TABLE (Relational Transformations)

Added in MySQL 8.0, `JSON_TABLE` converts nested JSON datasets into a virtual relational table structure.

```sql
SELECT p.name, jt.color
FROM products p,
JSON_TABLE(
    p.attributes,
    '$.colors[*]' COLUMNS(
        color VARCHAR(50) PATH '$'
    )
) AS jt;
```

### Advanced JSON_TABLE Example
```sql
SELECT p.name, jt.brand, jt.battery_hours
FROM products p,
JSON_TABLE(
    p.attributes,
    '$' COLUMNS(
        brand VARCHAR(50) PATH '$.brand',
        battery_hours INT PATH '$.specs.battery_hours'
    )
) AS jt;
```

---

## 6. Indexing JSON Columns

MySQL cannot index a full binary JSON column directly. You must create indexes using virtual columns or functional indexes.

### Method 1: Indexing a Generated Virtual Column
Create a virtual column that extracts a value from the JSON document, and then create an index on that virtual column.

```sql
-- 1. Alter table to add virtual column
ALTER TABLE products 
    ADD COLUMN product_brand VARCHAR(50) 
    GENERATED ALWAYS AS (attributes->>'$.brand') VIRTUAL;

-- 2. Index the virtual column
CREATE INDEX idx_products_brand ON products (product_brand);
```

### Method 2: Functional Index (MySQL 8.0.13+)
Index the extraction expression directly without creating a virtual column.
```sql
CREATE INDEX idx_products_spec_battery 
    ON products ((CAST(attributes->>'$.specs.battery_hours' AS UNSIGNED)));
```
> [!IMPORTANT]
> When using functional indexes with JSON, you must explicitly CAST the value to the correct target SQL type (like `UNSIGNED`, `CHAR`, etc.).
