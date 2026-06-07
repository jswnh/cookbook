## 1. Creating and Inserting JSON

### Table Schema

```sql
CREATE TABLE sales.products (
    id INT IDENTITY(1,1) PRIMARY KEY,
    name NVARCHAR(100) NOT NULL,
    attributes JSON NOT NULL -- Native JSON type (SQL Server 2022)
);
```

### Inserting JSON Data

```sql
INSERT INTO sales.products (name, attributes) VALUES (
    'Smart Watch',
    '{"brand": "TechCorp", "specs": {"water_proof": true, "battery_hours": 48}, "colors": ["black", "silver"]}'
);
```

---

## 2. Extracting JSON Properties

SQL Server provides two primary scalar functions for JSON extraction:

- **`JSON_VALUE(expression, path)`**: Extracts a **scalar** value (string, number, boolean) from a JSON document. Returns a scalar string.
- **`JSON_QUERY(expression, path)`**: Extracts an **object** or an **array** from a JSON document. Returns a JSON fragment string.

```sql
SELECT
    name,
    -- Extract scalar values (returns NVARCHAR)
    JSON_VALUE(attributes, '$.brand') AS brand,
    CAST(JSON_VALUE(attributes, '$.specs.battery_hours') AS INT) AS battery_hours,

    -- Extract JSON fragments (objects or arrays)
    JSON_QUERY(attributes, '$.specs') AS specs_object,
    JSON_QUERY(attributes, '$.colors') AS colors_array
FROM sales.products;
```

> [!NOTE]
> **Path Modes (`lax` vs `strict`)**:
> Paths default to `lax` mode. If a path is missing, `JSON_VALUE` returns `NULL`.
> If you specify `strict` mode, SQL Server raises an error if the path is missing.
> _Example_: `JSON_VALUE(attributes, 'strict $.specs.water_proof')`

---

## 3. Modifying JSON Documents

Use **`JSON_MODIFY(expression, path, newValue)`** to update, insert, or delete keys.

```sql
-- 1. Update existing value
UPDATE sales.products
SET attributes = JSON_MODIFY(attributes, '$.specs.battery_hours', 50)
WHERE id = 1;

-- 2. Add a new key
UPDATE sales.products
SET attributes = JSON_MODIFY(attributes, '$.model', 'TC-500')
WHERE id = 1;

-- 3. Delete a key (pass NULL as the newValue)
UPDATE sales.products
SET attributes = JSON_MODIFY(attributes, '$.specs.water_proof', NULL)
WHERE id = 1;

-- 4. Append to an array (requires append keyword)
UPDATE sales.products
SET attributes = JSON_MODIFY(attributes, 'append $.colors', 'gold')
WHERE id = 1;
```

---

## 4. Validating JSON

Use **`ISJSON(expression)`** to verify if a string contains valid JSON. SQL Server 2022 expands `ISJSON` to allow validation of specific JSON structures.

```sql
-- 1. Basic validation (returns 1 if valid, 0 if invalid)
SELECT ISJSON(attributes) FROM sales.products;

-- 2. Structure validation (SQL Server 2022+)
SELECT
    ISJSON(attributes, VALUE) AS is_any_json,
    ISJSON(attributes, OBJECT) AS is_json_object,
    ISJSON(attributes, ARRAY) AS is_json_array,
    ISJSON(attributes, SCALAR) AS is_json_scalar;
```

---

## 5. Converting JSON to Rows (OPENJSON)

`OPENJSON` is a table-valued function that parses JSON text and returns matching object keys/values as rows.

### Default Schema Output

Without a mapping clause, `OPENJSON` returns three columns: `key` (index or name), `value`, and `type`.

```sql
SELECT * FROM OPENJSON('{"brand": "TechCorp", "years": [2024, 2025]}');
-- Returns:
-- key   | value       | type
-- brand | TechCorp    | 1 (string)
-- years | [2024,2025] | 4 (array)
```

### With Explicit Schema Mapping (WITH Clause)

Converts JSON structures directly into rows matching database table fields.

```sql
SELECT p.name, jt.brand, jt.battery_hours
FROM sales.products p
CROSS APPLY OPENJSON(p.attributes)
WITH (
    brand NVARCHAR(50) '$.brand',
    battery_hours INT '$.specs.battery_hours',
    colors NVARCHAR(MAX) '$.colors' AS JSON -- AS JSON is required to output array text
) AS jt;
```

---

## 6. Formatting Rows as JSON (FOR JSON)

Appended to standard SQL queries to output data structured as JSON strings.

- **`FOR JSON AUTO`**: Automatically structures nested output based on the tables in the `FROM` list.
- **`FOR JSON PATH`**: Formats output based on dot-notation aliases.

```sql
SELECT
    id AS [customer.id],
    name AS [customer.name],
    created_at AS [customer.registered]
FROM sales.customers
FOR JSON PATH;
-- Output: [{"customer":{"id":1,"name":"John Doe","registered":"2026-06-07T..."}}]
```

---

## 7. Indexing JSON Properties

SQL Server cannot index JSON columns directly. To index a JSON property, you must create a computed column that extracts the key using `JSON_VALUE`, and then build an index on that computed column.

```sql
-- 1. Create a persisted computed column
ALTER TABLE sales.products
    ADD product_brand AS CAST(JSON_VALUE(attributes, '$.brand') AS NVARCHAR(50)) PERSISTED;

-- 2. Build index on the computed column
CREATE NONCLUSTERED INDEX idx_products_brand ON sales.products (product_brand);
```

> [!IMPORTANT]
> The computed column must be defined as `PERSISTED` (physically stored on disk) for SQL Server to allow creating indexes on it.
