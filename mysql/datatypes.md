## 1. Numeric Data Types

MySQL supports integer, fixed-point, and floating-point numeric types.

### Integer Types

| Type                  | Storage (Bytes) | Minimum Value (Signed)       | Maximum Value (Signed)      | Minimum Value (Unsigned) | Maximum Value (Unsigned)     |
| :-------------------- | :-------------- | :--------------------------- | :-------------------------- | :----------------------- | :--------------------------- |
| **`TINYINT`**         | 1               | `-128`                       | `127`                       | `0`                      | `255`                        |
| **`SMALLINT`**        | 2               | `-32,768`                    | `32,767`                    | `0`                      | `65,535`                     |
| **`MEDIUMINT`**       | 3               | `-8,388,608`                 | `8,388,607`                 | `0`                      | `16,777,215`                 |
| **`INT` / `INTEGER`** | 4               | `-2,147,483,648`             | `2,147,483,647`             | `0`                      | `4,294,967,295`              |
| **`BIGINT`**          | 8               | `-9,223,372,036,854,775,808` | `9,223,372,036,854,775,807` | `0`                      | `18,446,744,073,709,551,615` |

#### Integer Syntax Example

```sql
CREATE TABLE users (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    age TINYINT UNSIGNED NOT NULL,
    score SMALLINT SIGNED DEFAULT 0
);
```

> [!NOTE]
> The display width attribute (e.g., `INT(11)`) is deprecated in MySQL 8.0.17 and removed or ignored in newer versions. Use `INT` or `INT UNSIGNED` directly. `ZEROFILL` is also deprecated.

### Fixed-Point & Floating-Point Types

- **`DECIMAL(M, D)` / `NUMERIC(M, D)`**: Exact fixed-point numbers. `M` is the maximum number of digits (precision, up to 65), `D` is the number of digits to the right of the decimal point (scale, up to 30). Highly recommended for monetary values.
- **`FLOAT(P)`**: Floating-point number. Single precision (4 bytes) if $P \le 24$, double precision (8 bytes) if $25 \le P \le 53$.
- **`FLOAT`**: 4-byte single-precision float.
- **`DOUBLE` / `DOUBLE PRECISION`**: 8-byte double-precision float.

#### Decimals Syntax Example

```sql
CREATE TABLE products (
    price DECIMAL(10, 2) NOT NULL, -- Up to 99999999.99
    discount FLOAT DEFAULT 0.00
);
```

---

## 2. String & Binary Data Types

MySQL strings can contain text, binary data, or lists of values. The default character set in MySQL 8.0+ is `utf8mb4` with `utf8mb4_0900_ai_ci` collation.

### Character Strings

- **`CHAR(N)`**: Fixed-length string (0 to 255 characters). Padded with spaces when stored, spaces stripped upon retrieval.
- **`VARCHAR(N)`**: Variable-length string (0 to 65,535 characters, subject to maximum row size limit of 65,535 bytes).
- **`TINYTEXT`**: Non-binary string up to 255 bytes.
- **`TEXT`**: Non-binary string up to 65,535 bytes (64 KB).
- **`MEDIUMTEXT`**: Non-binary string up to 16,777,215 bytes (16 MB).
- **`LONGTEXT`**: Non-binary string up to 4,294,967,295 bytes (4 GB).

### Binary Strings

- **`BINARY(N)`**: Fixed-length binary string.
- **`VARBINARY(N)`**: Variable-length binary string.
- **`TINYBLOB`**: Binary Large Object up to 255 bytes.
- **`BLOB`**: Binary Large Object up to 65,535 bytes (64 KB).
- **`MEDIUMBLOB`**: Binary Large Object up to 16 MB.
- **`LONGBLOB`**: Binary Large Object up to 4 GB.

```sql
CREATE TABLE user_profiles (
    username VARCHAR(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL,
    bio TEXT,
    avatar_raw LONGBLOB
);
```

### Enumeration & Set Types

- **`ENUM('value1', 'value2', ...)`**: String object that can have exactly one value chosen from a list of permitted values defined at table creation (up to 65,535 elements).
- **`SET('value1', 'value2', ...)`**: String object that can have zero or more values, each of which must be chosen from the defined list (up to 64 distinct elements).

```sql
CREATE TABLE orders (
    status ENUM('pending', 'processing', 'shipped', 'cancelled') DEFAULT 'pending',
    notifications SET('email', 'sms', 'push') DEFAULT 'email'
);
```

---

## 3. Date & Time Data Types

MySQL supports several formats for handling date and time values.

| Type            | Format                         | Range                                                                | Storage   | Behavior / Description                                                       |
| :-------------- | :----------------------------- | :------------------------------------------------------------------- | :-------- | :--------------------------------------------------------------------------- |
| **`DATE`**      | `YYYY-MM-DD`                   | `1000-01-01` to `9999-12-31`                                         | 3 bytes   | Represents a date value without time.                                        |
| **`TIME`**      | `hh:mm:ss[.ffffff]`            | `-838:59:59` to `838:59:59`                                          | 3–6 bytes | Time of day or elapsed time interval. Supports fractional seconds.           |
| **`DATETIME`**  | `YYYY-MM-DD hh:mm:ss[.ffffff]` | `1000-01-01 00:00:00.000000` to `9999-12-31 23:59:59.999999`         | 5–8 bytes | Constant date & time. Unaffected by connection timezone changes.             |
| **`TIMESTAMP`** | `YYYY-MM-DD hh:mm:ss[.ffffff]` | `1970-01-01 00:00:01.000000` UTC to `2038-01-19 03:14:07.999999` UTC | 4–7 bytes | Timezone-aware date & time. Stored as UTC, converted to connection timezone. |
| **`YEAR`**      | `YYYY`                         | `1901` to `2155`                                                     | 1 byte    | A 4-digit year.                                                              |

#### Date / Time Examples

```sql
CREATE TABLE audit_log (
    event_name VARCHAR(100) NOT NULL,
    logged_date DATE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

---

## 4. JSON Data Type

MySQL provides a native `JSON` data type that parses documents into an optimized binary format for rapid lookups and validation.

- **Storage**: Similar to `LONGBLOB` or `LONGTEXT`, but optimized for fast document extraction.
- **Validation**: Rejects invalid JSON documents automatically.
- **JSON Functions**: Provides built-in operators (like `->` and `->>`) and functions.

```sql
CREATE TABLE system_config (
    id INT AUTO_INCREMENT PRIMARY KEY,
    metadata JSON NOT NULL
);
```

---

## 5. Spatial Data Types (GIS)

MySQL supports spatial types representing geometry values, following the OpenGIS specification.

- **Single Geometries**: `GEOMETRY`, `POINT`, `LINESTRING`, `POLYGON`
- **Collections**: `MULTIPOINT`, `MULTILINESTRING`, `MULTIPOLYGON`, `GEOMETRYCOLLECTION`

```sql
CREATE TABLE locations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    coordinate POINT NOT NULL SRID 4326, -- SRID 4326 is WGS 84 (GPS standard coordinates)
    SPATIAL INDEX(coordinate)
);
```

---

## 6. Miscellaneous Types

- **`BIT(M)`**: A bit-value type, where `M` is the number of bits per value (1 to 64). Default is 1.

```sql
CREATE TABLE settings (
    is_active BIT(1) DEFAULT b'1',
    permissions BIT(8) DEFAULT b'00000100'
);
```
