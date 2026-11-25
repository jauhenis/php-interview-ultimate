---
title: 'PHP 8.6 New Features'
slug: '/answers/php86_features'
---

# PHP 8.6 New Features

[PHP 8.6 release announcement](https://www.php.net/releases/8.6/en.php)

## Partial application and range limits

The expression `$limit = clamp(?, 1, 50);` creates a closure with fixed bounds. Calling `$limit(80)` fills the placeholder and returns `50`. Bounds can also be comparable objects such as `Time\Duration` instances, useful for limiting a retry delay.

## Sort order and readonly defaults

Pass `SortDirection::Descending` to request descending order from a method typed with `SortDirection`. Passing an integer raises a `TypeError`. A property can declare a fixed default directly:

```php
final class SortOptions
{
    public readonly SortDirection $direction = SortDirection::Ascending;
}
```

## Streams and network requests

Register a handle with `Io\Poll\Context` for `Io\Poll\Event::Read` to wait for readable data. The context’s `wait()` method returns watchers with events. Configure error handling separately: `StreamErrorMode::Exception` exposes the error code, wrapper name and path or URL through the exception.

When resending a streamed request body after a redirect, libcurl may need to rewind the stream. Supply a seek callback through `CURLOPT_SEEKFUNCTION` for this case. [PHP 8.6.0 changelog](https://www.php.net/ChangeLog-8.php#8.6.0)

## Inspecting parameters and properties

Use `ReflectionParameter::getDocComment()` to retrieve a DocComment attached directly to a parameter. Use `ReflectionProperty::isReadable()` and `isWritable()` to check access from a given scope. For example, callers outside a class can read a `public private(set)` property, but only the class can set it.

## What to check before upgrading

- Move `return` out of `finally` so exceptions can propagate.
- `mb_ereg()` and `mb_split()` are deprecated. Port the patterns to PCRE and use the `u` modifier for UTF-8 input.
- Replace `is_long()` and `is_integer()` with `is_int()`, and `doubleval()` with `floatval()`.
- Check the result of `file_put_contents()`: a failure while flushing or closing the stream now returns `false`.
- Review session configuration. Strict mode and HttpOnly cookies are enabled by default, and SameSite defaults to `Lax`. Custom session handlers that omit either `create_sid()` or `validateId()` are deprecated.
