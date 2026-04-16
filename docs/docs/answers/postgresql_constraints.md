---
title: 'PostgreSQL Constraints'
sidebar_label: 'PostgreSQL Constraints'
---

# PostgreSQL Constraints

Constraints allow you to define rules for the data in your tables.

## 1. Primary Key

Uniquely identifies each row in a table. It is a combination of `NOT NULL` and `UNIQUE`.

```sql
CREATE TABLE products (
    product_no integer PRIMARY KEY,
    name text
);
```

## 2. Foreign Key

Ensures referential integrity between tables.

```sql
CREATE TABLE orders (
    order_id integer PRIMARY KEY,
    product_no integer REFERENCES products (product_no) ON DELETE CASCADE
);
```

**Actions on Delete/Update:**

- `CASCADE`: Delete/update the referencing rows.
- `RESTRICT`: Prevent deletion/update of the referenced row.
- `SET NULL`: Set the referencing column to NULL.
- `SET DEFAULT`: Set the referencing column to its default value.

## 3. Unique Constraint

Ensures that all values in a column (or group of columns) are distinct.

```sql
ALTER TABLE users ADD CONSTRAINT unique_email UNIQUE (email);
```

## 4. Check Constraint

Ensures that values in a column satisfy a specific boolean expression.

```sql
CREATE TABLE products (
    price numeric CHECK (price > 0)
);
```

## 5. Not-Null Constraint

Ensures that a column cannot have a `NULL` value.

```sql
ALTER TABLE users ALTER COLUMN username SET NOT NULL;
```

---

_Back to [PostgreSQL](../questions/08-postgresql.mdx)_
