## 1. Numeric Data Types

PostgreSQL supports integers, arbitrary-precision decimals, and floating-point values.

| Name                          | Storage  | Description                      | Range                                                                |
| :---------------------------- | :------- | :------------------------------- | :------------------------------------------------------------------- |
| **`smallint`**                | 2 bytes  | Small-range integer              | `-32,768` to `32,767`                                                |
| **`integer`** (or `int`)      | 4 bytes  | Standard integer                 | `-2,147,483,648` to `2,147,483,647`                                  |
| **`bigint`**                  | 8 bytes  | Large-range integer              | `-9,223,372,036,854,775,808` to `9,223,372,036,854,775,807`          |
| **`numeric`** / **`decimal`** | Variable | User-specified precision (exact) | Up to 131,072 digits before decimal point; up to 16,383 digits after |
| **`real`**                    | 4 bytes  | Single-precision float (inexact) | 6 decimal digits precision                                           |
| **`double precision`**        | 8 bytes  | Double-precision float (inexact) | 15 decimal digits precision                                          |

### Serial Auto-increment Types (Legacy)

PG's legacy auto-increment mechanism. Creates an implicit sequence generator.

- **`smallserial`** (2 bytes, 1 to 32,767)
- **`serial`** (4 bytes, 1 to 2,147,483,647)
- **`bigserial`** (8 bytes, 1 to 9,223,372,036,854,775,807)

```sql
-- Legacy serial syntax
CREATE TABLE tasks (
    id SERIAL PRIMARY KEY,
    name TEXT
);
```

> [!NOTE]
> For PostgreSQL 10+, SQL standard `GENERATED AS IDENTITY` is preferred over `SERIAL`.

---

## 2. Character & Text Data Types

PostgreSQL stores strings using UTF-8 by default. Unlike other databases, there is no performance penalty for using `text` instead of `varchar`.

- **`varchar(n)`** / **`character varying(n)`**: Variable-length string with limit $n$.
- **`char(n)`** / **`character(n)`**: Fixed-length string, blank-padded.
- **`text`**: Variable-length string with no size limit (maximum 1 GB). **Highly recommended for general text fields.**

```sql
CREATE TABLE authors (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    biography TEXT -- No size limit, stored efficiently
);
```

---

## 3. Date & Time Data Types

PostgreSQL has robust timezone and interval parsing.

- **`timestamp`** (or `timestamp without time zone`): Date and time, stored without timezone adjustment.
- **`timestamptz`** (or `timestamp with time zone`): **Highly Recommended.** Input is converted and stored in UTC, and output is displayed in the local timezone of the current database session.
- **`date`**: Calendar date (year, month, day).
- **`time`**: Time of day without date.
- **`interval [fields]`**: Represents elapsed periods of time (e.g. `'2 hours 30 minutes'`).

```sql
CREATE TABLE events (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    event_time TIMESTAMPTZ NOT NULL,
    duration INTERVAL DAY TO HOUR NOT NULL
);

-- Query using interval logic
SELECT event_time + duration AS end_time
FROM events;
```

---

## 4. Specialized Data Types

### UUID Data Type

Stores native 128-bit UUIDs conforming to RFC 4122. Takes significantly less space (16 bytes) and performs faster than a `varchar(36)` representation.

```sql
CREATE TABLE sessions (
    session_id UUID PRIMARY KEY DEFAULT gen_random_uuid(), -- gen_random_uuid() is built-in (v13+)
    user_id INT
);
```

### Boolean Data Type

Explicit `boolean` state. Accepts `'true'`, `'t'`, `'y'`, `'yes'`, `'1'` for true and `'false'`, `'f'`, `'n'`, `'no'`, `'0'` for false.

```sql
CREATE TABLE users (
    is_active BOOLEAN DEFAULT TRUE
);
```

### Array Data Types

PostgreSQL allows fields to be defined as multidimensional arrays of any base type.

```sql
CREATE TABLE staff (
    name TEXT,
    phone_numbers TEXT[], -- Array of text
    pay_by_quarter INT[4] -- 1D array of 4 integers
);

-- Inserting array values
INSERT INTO staff VALUES ('Jane', '{"555-0100", "555-0199"}', '{2000, 2100, 2200, 2300}');
```

### JSON & JSONB

- **`json`**: Stores exact copy of input text. Requires re-parsing on query execution. Fast write, slow query.
- **`jsonb`**: **Highly Recommended.** Stores JSON in decomposed binary format. Removes whitespace, duplicates, and orders keys. Supports index lookups (GIN) and parses much faster during queries.

```sql
CREATE TABLE client_logs (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    payload JSONB NOT NULL
);
```

### Network Address Types

Optimized data structures representing IPv4/IPv6 addresses and MAC addresses. Validates input formatting and provides network operators (e.g. checking subnet containment).

- **`inet`**: Host address and subnet representation.
- **`cidr`**: Subnet network specification.
- **`macaddr`**: Hardware MAC addresses.

```sql
CREATE TABLE server_logs (
    ip_address INET,
    mac_address MACADDR
);
```

---

## 5. Custom Range & Enumeration Types

### Custom ENUM Types

Enums are created as schema-level types in PostgreSQL.

```sql
-- 1. Declare schema level enum type
CREATE TYPE order_status AS ENUM ('pending', 'processing', 'completed', 'cancelled');

-- 2. Use it inside tables
CREATE TABLE orders (
    id INT PRIMARY KEY,
    status order_status DEFAULT 'pending'
);
```

### Range Types

Represents intervals of values. Built-in ranges include `int4range`, `numrange`, `tsrange` (timestamp range), `tstzrange` (timestamptz range), etc.

```sql
CREATE TABLE hotel_reservations (
    room_number INT,
    booking_period TSTZRANGE -- range of timestamptz
);

-- Insert a reservation range
INSERT INTO hotel_reservations VALUES (
    101,
    tstzrange('2026-06-01 14:00:00+08', '2026-06-05 11:00:00+08')
);
```
