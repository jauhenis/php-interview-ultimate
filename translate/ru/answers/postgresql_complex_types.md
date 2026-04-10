# Сложные типы данных PostgreSQL

PostgreSQL предоставляет несколько продвинутых структур данных, выходящих за рамки стандартных реляционных типов.

## 1. Массивы (Arrays)

PostgreSQL позволяет определять столбцы таблицы как многомерные массивы переменной длины.

```sql
CREATE TABLE sal_emp (
    name            text,
    pay_by_quarter  integer[],
    schedule        text[][]
);

INSERT INTO sal_emp VALUES (
    'Bill',
    '{10000, 10000, 10000, 10000}',
    '{{"meeting", "lunch"}, {"training", "presentation"}}'
);
```

### Запросы к массивам
- Доступ к элементу: `pay_by_quarter[1]` (PostgreSQL по умолчанию использует индексацию с 1).
- Срез (slicing): `pay_by_quarter[1:2]`.

## 2. Перечисления (ENUM)

Перечисления — это типы данных, представляющие собой статический упорядоченный набор значений.

```sql
CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');

CREATE TABLE person (
    name text,
    current_mood mood
);
```

### Изменение перечислений
```sql
ALTER TYPE mood ADD VALUE 'excited' AFTER 'happy';
```

## 3. JSON и JSONB

- `json`: хранит точную копию входного текста.
- `jsonb`: хранит данные в разложенном бинарном формате. Он немного медленнее при вводе, но значительно быстрее при обработке и поддерживает индексацию.

---
*Назад к [PostgreSQL](../questions/24-postgresql.mdx)*
