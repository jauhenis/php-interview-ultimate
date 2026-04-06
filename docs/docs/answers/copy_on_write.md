---
title: "Copy-on-Write in PHP"
sidebar_label: "Copy-on-Write"
---

# Copy-on-Write in PHP

**Copy-on-write (CoW)** is a memory management optimization used by the Zend Engine to avoid unnecessary duplication of data. It ensures that multiple variables share the same memory until one of them is modified.

- **Shared Storage**: Multiple variables point to the same internal structure (`zval`) as long as they only perform read operations.
- **Lazy Copying**: Memory allocation for a new copy happens only at the moment of modification ("on write").

This behavior applies primarily to **arrays** and **strings**.

---

## 1. Memory Management and Passing by Value

A common misconception is that passing a large array to a function by value always creates a full copy, leading to high memory usage.

```php
function handle(array $array) {
    // Only reading here...
    return count($array);
}

$largeArray = range(0, 500_000);
handle($largeArray);
```

In this case, no copy is made. The parameter `$array` and the variable `$largeArray` share the same `zval`.

---

## 2. Modification and the Copy-on-Write Trigger

When a variable is modified, PHP checks if its `zval` is shared (refcount > 1). If it is, PHP "separates" the `zval` by creating a real copy for the modified variable.

```php
function handle(array $array) {
    $array[0] = 'new value'; // Copy is triggered here
    return $array;
}
```

### What triggers a copy?
- Changing an array element.
- Adding or removing elements (`unset()`).
- Modifying a character in a string.
- Sorting the array.

---

## 3. Measuring Peak Memory Usage

The following example demonstrates how peak memory remains stable during read operations but jumps when a modification occurs.

```php
<?php

function printMemory(string $header): void
{
    echo "{$header}: " . round(memory_get_peak_usage() / 1024) . " KB" . PHP_EOL;
}

function handleRead(array $array): void
{
    foreach ($array as $row) {
        $dummy = $row;
    }
    printMemory('Peak inside handleRead');
}

function handleWrite(array $array): void
{
    $array[0] = 'changed'; 
    printMemory('Peak inside handleWrite');
}

$largeArray = range(0, 500_000);
printMemory('Initial Peak');

handleRead($largeArray);
handleWrite($largeArray);
```

**Expected Results (approximate):**
- **Initial Peak**: 16,764 KB
- **Peak inside handleRead**: 16,764 KB (No copy)
- **Peak inside handleWrite**: 33,153 KB (Copy created)

---

## 4. Objects and Handles

Objects in PHP 5+ do **not** use the same copy-on-write mechanism for their properties. When an object is passed, PHP passes an **object handle** (identifier).

```php
function handle(stdClass $obj) {
    $obj->property = 'new value'; // Changes the original object
}
```

Modifying an object's property inside a function affects the original object because both variables point to the same object instance via the same handle. Therefore, duplicating a large object structure on modification does not happen automatically like it does with arrays.

---

## 5. Practical Takeaways

- **Avoid unnecessary references**: Do not use `&$array` just for "optimization." PHP's CoW handles memory efficiently for read-only access. Only use references when you explicitly need to modify the original variable.
- **Be careful with large arrays**: If you are working with massive datasets and need to modify them, consider memory-efficient alternatives like `Generators` or `SplFixedArray`.
- **Objects are efficient**: Passing objects is cheap because only the handle is passed.

---

## Sources and Further Reading

- [Copy-on-write в PHP](https://zualex.com/posts/copy-on-write-php/) — Aleksandr Zubarev (2021).
- [PHP Internals Book — Memory Management](https://www.phpinternalsbook.com/php7/internal_structure/zvals/memory_management.html)
- [PHP Manual — Objects and References](https://www.php.net/manual/en/language.oop5.references.php)
