---
title: 'PHP 8.5 New Features'
slug: '/answers/php85_features'
---

# PHP 8.5 New Features

[Official Documentation: PHP 8.5 Release](https://www.php.net/releases/8.5/en.php)

## 1. URI Extension

PHP 8.5 introduces a new built-in extension for URI parsing and manipulation, supporting both RFC 3986 and WHATWG URL standards.

**RFC 3986 Example:**

```php
use Uri\Rfc3986\Uri;

$uri = new Uri('https://thephp.foundation:443/sponsor/');
echo $uri->getHost(); // thephp.foundation
echo $uri->getPort(); // 443
```

**WHATWG Example:**

```php
use Uri\WhatWg\Url;

$url = new Url('https://example.com/path/../other');
echo $url->getPathname(); // /other
```

## 2. Pipe Operator (`|>`)

The pipe operator allows for a more readable syntax when chaining function calls. The result of the left-hand expression is passed as the first argument to the right-hand callable.

```php
$result = "  hello world  "
    |> trim(...)
    |> strtoupper(...)
    |> (fn($str) => str_replace('WORLD', 'PHP 8.5', $str));
// Result: "HELLO PHP 8.5"
```

## 3. Clone With

The `clone` keyword now supports an optional array of properties to update during the cloning process. This is particularly useful for immutable objects and `readonly` properties.

```php
final readonly class Response {
    public function __construct(
        public int $statusCode,
        public string $reasonPhrase,
    ) {}

    public function withStatus(int $code, string $reasonPhrase = ''): self {
        return clone ($this, [
            'statusCode' => $code,
            'reasonPhrase' => $reasonPhrase,
        ]);
    }
}
```

## 4. `#[\NoDiscard]` Attribute

A new built-in attribute that signals that the return value of a function or method must not be ignored.

```php
#[\NoDiscard]
function validateUser(): bool {
    // ...
    return true;
}

// Warning: Return value of validateUser() is ignored.
validateUser();

// To explicitly ignore, use (void) cast:
(void) validateUser();
```

## 5. Closures and First-Class Callables in Constant Expressions

Static closures and first-class callables (e.g., `strlen(...)`) can now be used in constant expressions, such as attribute parameters and class constants.

```php
class Formatter {
    public const string_formatter = strtoupper(...);

    #[MyAttribute(value: fn() => 'default')]
    public string $data;
}
```

## 6. New `array_first()` and `array_last()` functions

Native functions to get the first and last values of an array, returning `null` if the array is empty.

```php
$arr = ['a' => 1, 'b' => 2, 'c' => 3];
$first = array_first($arr); // 1
$last = array_last($arr);   // 3
```

## 7. Attribute Improvements

- `#[\Override]` can now target **properties**.
- `#[\Deprecated]` can be used on **traits** and **constants**.
- Attributes can now target **constants**.

## 8. Constructor Property Promotion: `final` Properties

Properties promoted in the constructor can now be marked as `final`.

```php
class User {
    public function __construct(
        public final string $id,
        public string $name,
    ) {}
}
```

## 9. Notable Deprecations

- Terminating `case` statements with a semicolon (use a colon `:` instead).
- Backticks as an alias for `shell_exec()`.
- `__sleep()` and `__wakeup()` magic methods (prefer `__serialize()` and `__unserialize()`).
- Non-standard cast names like `(boolean)` and `(integer)` (use `(bool)` and `(int)`).
- PIE (PHP Installer for Extensions) is now the recommended tool over PECL.
