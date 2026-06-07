## 1. Numeric Data Types

### Exact Numerics (Integers & Decimals)

| Name                               | Storage    | Range                                                                  |
| :--------------------------------- | :--------- | :--------------------------------------------------------------------- |
| **`bit`**                          | 1 byte     | `0`, `1`, or `NULL`. (Columns in a table are grouped into bytes).      |
| **`tinyint`**                      | 1 byte     | `0` to `255`                                                           |
| **`smallint`**                     | 2 bytes    | `-32,768` to `32,767`                                                  |
| **`int`**                          | 4 bytes    | `-2,147,483,648` to `2,147,483,647`                                    |
| **`bigint`**                       | 8 bytes    | `-9,223,372,036,854,775,808` to `9,223,372,036,854,775,807`            |
| **`decimal(p,s)`** / **`numeric`** | 5–17 bytes | Fixed-precision exact decimals. `p` (precision) up to 38, `s` (scale). |
| **`money`**                        | 8 bytes    | `-922,337,203,685,477.5808` to `922,337,203,685,477.5807`              |
| **`smallmoney`**                   | 4 bytes    | `-214,748.3648` to `214,748.3647`                                      |

### Approximate Numerics (Floating-Point)

- **`float(n)`**: Inexact floating point. Storage is 4 bytes for $n \le 24$ (7 digits precision) and 8 bytes for $25 \le n \le 53$ (15 digits precision).
- **`real`**: Synonym for `float(24)` (4 bytes).

---

## 2. Character & String Data Types

SQL Server distinguishes between standard ASCII/UTF-8 strings and Unicode (UCS-2/UTF-16) strings.

- **`char(n)`**: Fixed-length non-Unicode string (up to 8,000 characters).
- **`varchar(n)`**: Variable-length non-Unicode string (up to 8,000 characters).
- **`varchar(max)`**: Large variable-length non-Unicode string (up to 2 GB). Replaces legacy `text`.
- **`nchar(n)`**: Fixed-length Unicode string (up to 4,000 characters).
- **`nvarchar(n)`**: Variable-length Unicode string (up to 4,000 characters).
- **`nvarchar(max)`**: Large variable-length Unicode string (up to 2 GB). Replaces legacy `ntext`.

> [!NOTE]
> **Unicode vs. Non-Unicode (The `N` prefix)**:
> Standard `varchar` stores characters in 1 byte, which historically could not represent multi-lingual characters. `nvarchar` uses 2 bytes (UCS-2/UTF-16) to support Unicode.
> **UTF-8 in SQL Server**: Starting in SQL Server 2019, you can store UTF-8 directly in standard `varchar(n)` (saving space for western text) by choosing a collation ending with `_UTF8`.

---

## 3. Date & Time Data Types

SQL Server has multiple temporal formats. **`datetime2`** is preferred over legacy `datetime` due to its larger range and accuracy.

| Name                    | Format                                 | Range                                    | Precision | Storage    |
| :---------------------- | :------------------------------------- | :--------------------------------------- | :-------- | :--------- |
| **`date`**              | `YYYY-MM-DD`                           | `0001-01-01` to `9999-12-31`             | 1 day     | 3 bytes    |
| **`time`**              | `hh:mm:ss[.nnnnnnn]`                   | `00:00:00.0000000` to `23:59:59.9999999` | 100 ns    | 3–5 bytes  |
| **`datetime2`**         | `YYYY-MM-DD hh:mm:ss[.nnnnnnn]`        | `0001-01-01` to `9999-12-31`             | 100 ns    | 6–8 bytes  |
| **`datetimeoffset`**    | `YYYY-MM-DD hh:mm:ss[.nnnnnnn] ±hh:mm` | `0001-01-01` to `9999-12-31`             | 100 ns    | 8–10 bytes |
| **`datetime`** (Legacy) | `YYYY-MM-DD hh:mm:ss[.nnn]`            | `1753-01-01` to `9999-12-31`             | 3.33 ms   | 8 bytes    |

```sql
CREATE TABLE audit_log (
    event_id INT IDENTITY(1,1) PRIMARY KEY,
    event_utc DATETIME2 DEFAULT SYSUTCDATETIME(),
    user_local_time DATETIMEOFFSET DEFAULT SYSDATETIMEOFFSET()
);
```

---

## 4. Binary Data Types

- **`binary(n)`**: Fixed-length binary data (up to 8,000 bytes).
- **`varbinary(n)`**: Variable-length binary data (up to 8,000 bytes).
- **`varbinary(max)`**: Large variable-length binary data (up to 2 GB). Replaces legacy `image`. Highly recommended for files/blobs.

```sql
CREATE TABLE documents (
    doc_id UNIQUEIDENTIFIER DEFAULT NEWID() PRIMARY KEY,
    file_content VARBINARY(MAX)
);
```

---

## 5. Specialized Data Types

### Uniqueidentifier (GUID)

Stores 16-byte globally unique identifiers. Generated using `NEWID()` (random UUIDv4) or `NEWSEQUENTIALID()` (ordered UUIDs to prevent index fragmentation).

```sql
CREATE TABLE sessions (
    session_id UNIQUEIDENTIFIER DEFAULT NEWID() PRIMARY KEY,
    user_id INT
);
```

### XML Type

Natively stores XML data. Allows validation via XML Schemas and supports querying via XPath/XQuery functions.

```sql
CREATE TABLE system_events (
    id INT IDENTITY(1,1) PRIMARY KEY,
    details XML
);
```

### JSON Data Type (SQL Server 2022+)

Historically, SQL Server stored JSON as standard `NVARCHAR(MAX)` and validated it using check constraints. SQL Server 2022 introduces a native binary **`JSON`** data type which stores JSON documents in an optimized format for fast lookups.

```sql
CREATE TABLE user_metadata (
    id INT IDENTITY(1,1) PRIMARY KEY,
    profile_data JSON -- Native JSON type
);
```

### Rowversion (Timestamp)

An automatically generated, unique binary number that changes whenever a row is updated. It is used as a mechanism for version-stamping table rows (optimistic concurrency checks).

```sql
CREATE TABLE inventory (
    item_id INT PRIMARY KEY,
    quantity INT,
    row_ver ROWVERSION -- Increments on any row edit
);
```
