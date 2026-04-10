---
title: "PHP 8.5 New Features"
slug: "/answers/php85_features"
---

# PHP 8.5 New Features

[Official Documentation: PHP 8.5 Release](https://www.php.net/releases/8.5/en.php)

## 1. URI Extension

PHP 8.5 introduces a new built-in extension for URI parsing and manipulation. This replaces many manual `parse_url` calls and custom regex-based URI parsing with a more robust, object-oriented approach.

```php
use URI\URI;

$uri = URI::parse("https://user:pass@example.com:8080/path?query=1#hash");
echo $uri->getHost(); // example.com
echo $uri->getPort(); // 8080
```

## 2. Pipe Operator (`|>`)

The pipe operator allows for a more readable syntax when chaining function calls. The result of the left-hand expression is passed as the first argument to the right-hand function call.

```php
$result = "  hello world  "
    |> 'trim'
    |> 'strtoupper'
    |> 'str_replace'('WORLD', 'PHP 8.5', ...); // "HELLO PHP 8.5"
```

## 3. `#[\NoDiscard]` Attribute

A new built-in attribute that signals to static analysis tools and IDEs that the return value of a function or method must not be ignored.

```php
#[\NoDiscard]
function validateUser(): bool {
    // ...
    return true;
}

// Warning: Return value of validateUser() is ignored.
validateUser();
```

## 4. Closures and First-Class Callables in Constant Expressions

Closures and first-class callables (e.g., `strlen(...)`) can now be used in constant expressions, such as `const` declarations, class constants, and property defaults.

```php
class Formatter {
    public const string_formatter = strtoupper(...);
    private Closure $validator = fn($v) => !empty($v);
}
```

## 5. New `array_first()` and `array_last()` functions

PHP 8.5 finally adds native functions to get the first and last elements of an array without moving the internal array pointer.

```php
$arr = ['a' => 1, 'b' => 2, 'c' => 3];
$first = array_first($arr); // 1
$last = array_last($arr);   // 3
```

## 6. cURL: New `curl_multi_get_handles()` function

A new function for the cURL extension that allows retrieving all handles associated with a multi handle.

```php
$multiHandle = curl_multi_init();
// ... add some handles ...
$handles = curl_multi_get_handles($multiHandle);
```

## 7. Filter: Exceptions on validation failures

The `filter_var` and related functions now support throwing exceptions on validation failures using a new flag.

```php
try {
    filter_var("invalid-email", FILTER_VALIDATE_EMAIL, FILTER_THROW_ON_FAILURE);
} catch (FilterException $e) {
    echo $e->getMessage();
}
```

## 8. New `max_memory_limit` INI directive

This directive allows setting a ceiling for `memory_limit` that cannot be exceeded by `ini_set()` calls at runtime.

## 9. PIE (PHP Installer for Extensions)

PHP 8.5 marks the official transition from the legacy **PECL** to **PIE**. PIE is a modern, PHAR-based installer for PHP extensions that works similarly to Composer.

- **GitHub Repository:** [https://github.com/php/pie](https://github.com/php/pie)
- **Usage:** `pie install <extension-name>`
