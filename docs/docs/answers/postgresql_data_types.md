---
title: 'PostgreSQL Data Types'
sidebar_label: 'PostgreSQL Data Types'
---

# PostgreSQL Data Types

PostgreSQL supports a wide range of data types, categorized into several groups.

## 1. Numeric Types

| Name                  | Storage Size | Description                     | Range                                                              |
| :-------------------- | :----------- | :------------------------------ | :----------------------------------------------------------------- |
| `smallint`            | 2 bytes      | small-range integer             | -32768 to +32767                                                   |
| `integer`             | 4 bytes      | typical choice for integer      | -2147483648 to +2147483647                                         |
| `bigint`              | 8 bytes      | large-range integer             | -9223372036854775808 to +9223372036854775807                       |
| `decimal` / `numeric` | variable     | user-specified precision, exact | up to 131072 digits before decimal point; up to 16383 digits after |
| `real`                | 4 bytes      | variable-precision, inexact     | 6 decimal digits precision                                         |
| `double precision`    | 8 bytes      | variable-precision, inexact     | 15 decimal digits precision                                        |
| `smallserial`         | 2 bytes      | small autoincrementing integer  | 1 to 32767                                                         |
| `serial`              | 4 bytes      | autoincrementing integer        | 1 to 2147483647                                                    |
| `bigserial`           | 8 bytes      | large autoincrementing integer  | 1 to 9223372036854775807                                           |

## 2. Character Types

| Name                                  | Description                   |
| :------------------------------------ | :---------------------------- |
| `character(n)` / `char(n)`            | fixed-length, blank padded    |
| `character varying(n)` / `varchar(n)` | variable-length with limit    |
| `text`                                | variable-length with no limit |

**Note:** In PostgreSQL, there is no performance difference between these three types.

## 3. Date/Time Types

| Name                       | Storage Size | Description                        |
| :------------------------- | :----------- | :--------------------------------- |
| `timestamp`                | 8 bytes      | both date and time (no time zone)  |
| `timestamp with time zone` | 8 bytes      | both date and time, with time zone |
| `date`                     | 4 bytes      | date (no date)                     |
| `time`                     | 8 bytes      | time of day (no date)              |
| `interval`                 | 16 bytes     | time interval                      |

## 4. Boolean Type

The `boolean` type can have the state `true`, `false`, or `unknown` (represented by `NULL`).

## 5. Other Notable Types

- **Geometric Types:** `point`, `line`, `lseg`, `box`, `path`, `polygon`, `circle`.
- **Network Address Types:** `cidr`, `inet`, `macaddr`.
- **JSON Types:** `json` (textual), `jsonb` (binary, indexed).
- **UUID:** `uuid` for universally unique identifiers.
- **XML:** `xml` for XML data.

---

_Back to [PostgreSQL](../questions/09-postgresql.mdx)_
