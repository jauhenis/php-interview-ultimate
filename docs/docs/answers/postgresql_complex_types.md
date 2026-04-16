---
title: 'PostgreSQL Complex Data Types'
sidebar_label: 'PostgreSQL Complex Data Types'
---

# PostgreSQL Complex Data Types

PostgreSQL provides several advanced data structures that go beyond standard relational types.

## 1. Arrays

PostgreSQL allows columns of a table to be defined as variable-length multidimensional arrays.

```sql
CREATE TABLE sal_emp (
    name            text,
    pay_by_quarter  integer[],
    schedule        text[][]
);

INSERT INTO sal_emp VALUES (
    'Bill',
    '{10000, 10000, 10000, 10000}',
    '{ { "meeting", "lunch" }, { "training", "presentation" } }'
);
```

### Querying Arrays

- Accessing an element: `pay_by_quarter[1]` (PostgreSQL uses 1-based indexing by default).
- Slicing: `pay_by_quarter[1:2]`.

## 2. Enumerated Types (ENUM)

Enums are data types that comprise a static, ordered set of values.

```sql
CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');

CREATE TABLE person (
    name text,
    current_mood mood
);
```

### Modifying Enums

```sql
ALTER TYPE mood ADD VALUE 'excited' AFTER 'happy';
```

## 3. JSON and JSONB

- `json`: Stores an exact copy of the input text.
- `jsonb`: Stores data in a decomposed binary format. It is slightly slower to input but significantly faster to process and supports indexing.

---

_Back to [PostgreSQL](../questions/08-postgresql.mdx)_
