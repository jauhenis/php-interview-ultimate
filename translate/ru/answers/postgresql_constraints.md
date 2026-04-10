# Ограничения PostgreSQL

Ограничения позволяют определять правила для данных в ваших таблицах.

## 1. Первичный ключ (Primary Key)

Уникально идентифицирует каждую строку в таблице. Это комбинация `NOT NULL` и `UNIQUE`.

```sql
CREATE TABLE products (
    product_no integer PRIMARY KEY,
    name text
);
```

## 2. Внешний ключ (Foreign Key)

Обеспечивает ссылочную целостность между таблицами.

```sql
CREATE TABLE orders (
    order_id integer PRIMARY KEY,
    product_no integer REFERENCES products (product_no) ON DELETE CASCADE
);
```

**Действия при удалении/обновлении:**
- `CASCADE`: удалить/обновить ссылающиеся строки.
- `RESTRICT`: запретить удаление/обновление ссылочной строки.
- `SET NULL`: установить в ссылающемся столбце значение NULL.
- `SET DEFAULT`: установить в ссылающемся столбце его значение по умолчанию.

## 3. Ограничение уникальности (Unique Constraint)

Гарантирует, что все значения в столбце (или группе столбцов) уникальны.

```sql
ALTER TABLE users ADD CONSTRAINT unique_email UNIQUE (email);
```

## 4. Ограничение проверки (Check Constraint)

Гарантирует, что значения в столбце удовлетворяют определённому логическому выражению.

```sql
CREATE TABLE products (
    price numeric CHECK (price > 0)
);
```

## 5. Ограничение Not-Null

Гарантирует, что столбец не может иметь значение `NULL`.

```sql
ALTER TABLE users ALTER COLUMN username SET NOT NULL;
```

---
*Назад к [PostgreSQL](../questions/24-postgresql.mdx)*
