# MySQL vs PostgreSQL

This page provides a comparison between MySQL and PostgreSQL, two of the most popular open-source relational database management systems (RDBMS).

## Overview

| Feature | MySQL | PostgreSQL |
| :--- | :--- | :--- |
| **Type** | Relational (RDBMS) | Object-Relational (ORDBMS) |
| **License** | Dual-licensed (GPL/Commercial) | PostgreSQL License (similar to MIT/BSD) |
| **SQL Compliance** | Partial (aims for ease of use) | High (strict adherence to standards) |
| **ACID Compliance** | Yes (with InnoDB) | Yes (fully ACID compliant) |
| **Concurrency** | Good (MVCC) | Excellent (Advanced MVCC) |
| **Extensibility** | Limited | High (Custom types, functions, operators) |

## Key Differences

### 1. Data Types
- **MySQL:** Standard types (INT, VARCHAR, TEXT, DATETIME, etc.). Limited support for complex types.
- **PostgreSQL:** Rich set of built-in types (Geometric, Network Address, JSONB, Arrays, Ranges, Enums). Supports custom types.

### 2. Performance & Concurrency
- **MySQL:** Traditionally known for high-speed read-heavy workloads. Uses row-level locking with InnoDB.
- **PostgreSQL:** Excels in complex queries, write-heavy workloads, and high concurrency. Uses MVCC (Multi-Version Concurrency Control) more extensively.

### 3. Features
- **MySQL:** Focuses on speed and simplicity. Features like `JOIN` and `INDEX` are well-optimized.
- **PostgreSQL:** Advanced features like Window Functions, Common Table Expressions (CTE), Full-Text Search, and advanced indexing (GIN, GiST, BRIN).

### 4. JSON Support
- **MySQL:** Supports JSON data type with functional indexes.
- **PostgreSQL:** Superior JSON support with `JSONB` (binary format), which allows for efficient indexing and querying of nested data.

### 5. Replication
- **MySQL:** Strong built-in replication (Master-Slave, Master-Master).
- **PostgreSQL:** Supports synchronous, asynchronous, and logical replication.

## When to Choose Which?

### Choose MySQL if:
- You need a simple, easy-to-set-up database for a standard web application.
- Your workload is primarily read-heavy.
- You are using a CMS like WordPress, Drupal, or Joomla.

### Choose PostgreSQL if:
- You have complex data structures or need custom types.
- You require strict data integrity and SQL compliance.
- Your application involves complex analytical queries (BI, Data Science).
- You need advanced features like full-text search or geographic data (PostGIS).

## Summary Table

| Feature | MySQL | PostgreSQL |
| :--- | :--- | :--- |
| **Joins** | Optimized for simple joins | Better for complex, multi-table joins |
| **Indexes** | B-Tree, Hash, Spatial, Full-text | B-Tree, Hash, GiST, GIN, BRIN, SP-GiST |
| **Stored Procedures** | Supported (SQL-based) | Advanced (PL/pgSQL, Python, Perl, etc.) |
| **Triggers** | Basic support | Advanced support (per-row, per-statement) |

---
*Back to [MySQL & Databases](../questions/08-mysql-databases.mdx) or [PostgreSQL](../questions/09-postgresql.mdx)*
