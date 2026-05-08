---
title: 'PHP Data Types'
slug: '/answers/data_types'
---

# PHP Data Types

PHP supports ten primitive types.

## Scalar Types

- **bool**: Boolean (true or false).
- **int**: Integer (whole numbers).
- **float**: Floating-point number (decimal numbers). **Important:** Floats have limited precision and are stored in binary (IEEE 754). Comparing them for equality is unreliable (e.g., `0.1 + 0.2 != 0.3`). For precise math (like money), use `BCMath` in PHP and `DECIMAL` in databases. [Detailed Floating Point Explained](/answers/floating_point_numbers)
- **string**: A sequence of characters.

## Compound Types

- **array**: An ordered map that associates values to keys.
- **object**: An instance of a class.
- **callable**: A type that can be called as a function.
- **iterable**: Any type that can be iterated over with `foreach` (array or object implementing `Traversable`).

## Special Types

- **resource**: A special variable holding a reference to an external resource (e.g., database connection, file handle).
- **null**: A variable that has no value.

## Type Conversion

PHP is a loosely typed language, but you can cast types using `(int)`, `(bool)`, `(float)`, `(string)`, `(array)`, `(object)`.

```php
$foo = "10";
$bar = (int)$foo; // $bar is now an integer 10
```

## Special values considered FALSE

When converting to boolean, these are false:

- the boolean `false`
- integer `0` and `-0`
- float `0.0` and `-0.0`
- empty string `""`, and string `"0"`
- array with zero elements
- type `NULL`
- the `SimpleXML` object created from empty tags

## Passing by Reference

Variables can be passed by reference by prefixing them with `&`. In some cases, like passing an argument to a function, you can ensure the function modifies the original variable.

Note: you cannot pass literals or constants by reference because they are not variables. However, assigning them as default values is perfectly valid: if the argument is omitted, PHP simply creates a temporary local variable under the hood to hold that default value.

## Type Hinting & Strict Types

- **Scalar Type Hints**: Introduced in PHP 7, allows you to specify the expected type of parameters and return values.
- **`declare(strict_types=1);`**: Enforces strict typing, meaning PHP will throw a `TypeError` if types do not match exactly (instead of casting them automatically).

## Union, Intersection & DNF Types

- **Union Types (PHP 8.0)**: Allows multiple types for a single variable/return value. `string|int`.
- **Intersection Types (PHP 8.1)**: Requires multiple types (interfaces/classes) to be present at the same time. `Countable&Iterator`.
- **DNF Types (PHP 8.2)**: Disjunctive Normal Form, allows combining Intersection and Union types. `(Countable&Iterator)|null`.
