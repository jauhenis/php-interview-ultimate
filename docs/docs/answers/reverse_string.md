---
title: 'Reversing a String in PHP'
sidebar_label: 'Reversing a String'
---

# Reversing a String in PHP

Reversing a string is a common algorithm task. Depending on your level of expertise, you might choose different approaches.

## 1. Junior Level: Built-in Function

The simplest and most efficient way for basic ASCII strings is using the built-in `strrev()` function.

```php
function reverseString(string $str): string
{
    return strrev($str);
}

echo reverseString("hello"); // "olleh"
```

## 2. Middle Level: Manual Loop

A manual implementation shows you understand that a string can be treated as an array of bytes. However, this method is **not multi-byte safe** (it will break characters like emoji or Cyrillic).

```php
function reverseStringLoop(string $str): string
{
    $reversed = '';
    $length = strlen($str);
    for ($i = $length - 1; $i >= 0; $i--) {
        $reversed .= $str[$i];
    }
    return $reversed;
}
```

## 3. Senior Level: Multi-byte (UTF-8) Safe

Modern applications often use UTF-8. Built-in `strrev()` and manual byte-loops will corrupt multi-byte characters. A Senior developer should be aware of this and provide a solution using multi-byte aware functions like `mb_str_split()`.

### Using `mb_str_split()`

This method is efficient and flexible for handling UTF-8 characters correctly.

```php
/**
 * Reverse a multi-byte string.
 */
function mb_strrev(string $string, string $encoding = null): string
{
    $chars = mb_str_split($string, 1, $encoding ?: mb_internal_encoding());
    return implode('', array_reverse($chars));
}

echo mb_strrev("Привет"); // "тевирП"
echo mb_strrev("👋🌍");   // "🌍👋"
```

### Using recursion (Alternative)

While not the most efficient for large strings, it demonstrates algorithmic thinking.

```php
function reverseRecursive(string $str): string
{
    if (mb_strlen($str) <= 1) {
        return $str;
    }
    return reverseRecursive(mb_substr($str, 1)) . mb_substr($str, 0, 1);
}
```
