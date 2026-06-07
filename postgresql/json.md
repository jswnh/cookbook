# PostgreSQL JSONB Reference (v16 / v17)

PostgreSQL provides native JSON support via `json` and `jsonb` data types. `jsonb` (Binary JSON) is highly recommended for almost all use cases due to its optimized storage footprint, fast query parsing, and indexing capabilities.

---

## 1. Creating and Inserting JSONB Data

### Table Schema
```sql
CREATE TABLE products (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name TEXT NOT NULL,
    attributes JSONB NOT NULL
);
```

### Inserting JSONB
```sql
INSERT INTO products (name, attributes) VALUES (
    'Smart Watch',
    '{"brand": "TechCorp", "specs": {"water_proof": true, "battery_hours": 48}, "colors": ["black", "silver"]}'
);
```

---

## 2. Extraction Operators

PostgreSQL has specialized operators for navigating nested JSON objects and arrays.

| Operator | Return Type | Description | Example |
| :--- | :--- | :--- | :--- |
| **`->`** | `jsonb` | Gets JSON object field by key or array index. | `attributes->'specs'` |
| **`->>`** | `text` | Gets JSON object field/array index as text. | `attributes->>'brand'` |
| **`#>`** | `jsonb` | Gets JSON object at specified path. | `attributes#>{'specs', 'battery_hours'}` |
| **`#>>`** | `text` | Gets JSON object at path as text. | `attributes#>>{'specs', 'battery_hours'}` |

### Extraction Examples
```sql
-- Query nested numeric values and cast them
SELECT 
    name,
    attributes->>'brand' AS brand,
    (attributes#>>'{specs,battery_hours}')::int AS battery_life
FROM products
WHERE (attributes->'specs'->>'water_proof')::boolean = TRUE;
```

---

## 3. JSONB Containment & Existence Operators

These operators allow checking for matching elements within JSONB arrays or objects. They are fully compatible with standard GIN indexes.

| Operator | Description | Example Query |
| :--- | :--- | :--- |
| **`@>`** | Left JSONB contains right JSONB document. | `attributes @> '{"brand": "TechCorp"}'` |
| **`<@`** | Left JSONB is contained by right JSONB. | `'{"brand": "TechCorp"}' <@ attributes` |
| **`?`** | Key/element string exists as a top-level property. | `attributes ? 'colors'` |
| **`?\|`** | Any of the key strings exist as top-level properties. | `attributes ?\| array['colors', 'options']` |
| **`?&`** | All key strings exist as top-level properties. | `attributes ?& array['brand', 'specs']` |

```sql
-- Find products containing 'black' in the colors array
SELECT name FROM products WHERE attributes->'colors' @> '["black"]';
```

---

## 4. Modifying JSONB Documents

JSONB objects can be combined, updated, or stripped of properties using mathematical operators and functions.

### Operators for Modification
- **`||` (Concatenate / Merge)**: Merges two JSONB documents. If keys overlap, the right-hand value overwrites the left.
- **`-` (Delete Key)**: Deletes a key from an object, or an element from an array.
- **`#-` (Delete Path)**: Deletes a nested key at a specified path.

```sql
-- 1. Merge a new property
UPDATE products 
SET attributes = attributes || '{"model": "TC-500"}' 
WHERE id = 1;

-- 2. Delete the 'brand' key
UPDATE products 
SET attributes = attributes - 'brand' 
WHERE id = 1;

-- 3. Delete a nested property
UPDATE products 
SET attributes = attributes #- '{specs, water_proof}' 
WHERE id = 1;
```

### Functions for Modification
- **`jsonb_set(target, path, new_value[, create_missing])`**: Replaces or inserts a key at a path.
- **`jsonb_strip_nulls(from_json)`**: Recursively removes keys containing NULL values.

```sql
-- Set battery_hours to 50
UPDATE products
SET attributes = jsonb_set(attributes, '{specs, battery_hours}', '50'::jsonb)
WHERE id = 1;
```

---

## 5. JSON Path Expressions (SQL Standard, PG 12+)

SQL standard JSON path syntax allows complex filtering and extraction patterns.

- **`jsonb_path_exists(target, path)`**: Returns boolean check.
- **`jsonb_path_query(target, path)`**: Extracts matching objects or values as rows.

```sql
-- Check if a product has a battery rating over 40 hours
SELECT name 
FROM products 
WHERE jsonb_path_exists(attributes, '$.specs.battery_hours ? (@ > 40)');

-- Extract all items from colors array as row results
SELECT name, jsonb_path_query(attributes, '$.colors[*]') AS color 
FROM products;
```

---

## 6. Indexing JSONB Columns

GIN (Generalized Inverted Index) allows indexing every key and value inside a JSONB column.

### Method 1: Default GIN Index (`jsonb_ops`)
Supports containment (`@>`), existence (`?`), and set queries (`?|`, `?&`).
```sql
CREATE INDEX idx_products_attributes_gin ON products USING gin (attributes);
```

### Method 2: Path GIN Index (`jsonb_path_ops`)
Only indexes hashes of paths and values.
- **Benefits**: Index is much smaller and search performance is faster than the default GIN index.
- **Limitations**: Only supports containment queries (`@>`), not existence checks (`?`).
```sql
CREATE INDEX idx_products_attributes_path_gin ON products USING gin (attributes jsonb_path_ops);
```

### Method 3: B-Tree Index on Expression
If you query one key frequently and need ordering or exact matches, create a standard B-Tree functional index.
```sql
CREATE INDEX idx_products_brand ON products ((attributes->>'brand'));
```
