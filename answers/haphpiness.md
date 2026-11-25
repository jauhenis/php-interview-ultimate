# HaPHPiness - Best things in PHP

This guide is inspired by [haphpiness.com](https://haphpiness.com/), highlighting the modern features that make PHP a great language to work with.

## Modern Language Features

### 1. Named Arguments (PHP 8.0)
Named arguments allow you to pass values to a function by specifying the parameter name, so that you don't have to rely on their order.
```php
// After: named arguments
htmlspecialchars($string, double_encode: false);
```

### 2. Union Types (PHP 8.0) and Intersection Types (PHP 8.1)
Union types allow variables to have more than one type. Intersection types require a value to satisfy multiple type constraints.
```php
// Union types
function process(int|string $input): void {}

// Intersection types
function save(Countable&Iterator $collection): void {}
```

### 3. Enums (PHP 8.1)
First-class support for enumerations, providing type safety for fixed sets of values.
```php
enum Status: string {
    case Active = 'active';
    case Inactive = 'inactive';
}
```

<a name="string-functions"></a>

### 4. str_contains(), str_starts_with(), str_ends_with() (PHP 8.0)
Consistent and readable functions for common string operations.
```php
if (str_contains($url, 'https')) { ... }
if (str_starts_with($file, '/var/www')) { ... }
if (str_ends_with($file, '.php')) { ... }
```

<a name="array-unpacking"></a>

### 5. Array Unpacking with String Keys (PHP 8.1)
The spread operator now works with string keys, making array merging clean and expressive.
```php
$config = [...$defaults, ...$custom];
```

### 6. Fibers (PHP 8.1)
Lightweight, cooperatively-scheduled coroutines for building async primitives.
```php
$fiber = new Fiber(function (): void {
    $value = Fiber::suspend('paused');
    echo "Resumed with: $value";
});
```

<a name="dev-server"></a>

### 8. Built-in development server (php -S)
No Apache or Nginx config needed for local development. Just run:
```bash
php -S localhost:8000
```

<a name="arrow-functions"></a>

### 10. Arrow Functions (PHP 7.4)
Concise syntax for short closures with automatic variable capture from parent scope.
```php
$doubled = array_map(fn($n) => $n * 2, $numbers);
```

<a name="match-expressions"></a>

### 11. Match Expressions (PHP 8.0)
A more powerful and safer alternative to `switch`, providing strict comparison and returning a value.
```php
$text = match($statusCode) {
    200, 201 => 'Success',
    404      => 'Not Found',
    default  => 'Unknown',
};
```

<a name="null-coalescing"></a>

### 12. Null coalescing operator ?? and ??= (PHP 7.0+)
Clean way to handle defaults and lazy initialization.
```php
$username = $_GET['user'] ?? 'anonymous';
$this->logger ??= new NullLogger();
```

### 15. Readonly properties and classes (PHP 8.1+)
Engine-enforced immutability for properties and entire classes.
```php
readonly class Money {
    public function __construct(public int $amount, public string $currency) {}
}
```

<a name="attributes"></a>

### 17. Attributes — native metadata
PHP 8.0 replaced docblock annotations with real syntax, checkable by static analysis.
```php
#[Route('/users', method: 'GET')]
public function index(): Response { ... }
```

### 34. Property hooks (PHP 8.4)
Define `get` and `set` behavior directly on property declarations, eliminating getter/setter boilerplate.
```php
class User {
    public string $fullName {
        get => $this->firstName . ' ' . $this->lastName;
    }
}
```

### 39. Pipe operator `|>` (PHP 8.5)
Left-to-right data flow for function chaining.
```php
$slug = $title
    |> trim(...)
    |> strtolower(...);
```

<a name="nodiscard"></a>

### 43. #[\NoDiscard] (PHP 8.5)
Warns when the return value of a function is ignored, essential for validation results or immutable operations.
```php
#[\NoDiscard]
function validate(array $data): bool { ... }
```

<a name="fatal-error-backtraces"></a>

### 44. Fatal error backtraces
PHP 8.5 gives you stack traces on fatal errors like "Maximum execution time exceeded", making production debugging much easier.

<a name="uri-extension"></a>

### 45. URI extension — proper URL parsing at last
PHP 8.5 introduces a standards-compliant URI extension with immutable URL objects.
```php
use Uri\Rfc3986\Uri;
$uri = new Uri('https://example.com/path');
```

<a name="array-is-list"></a>

### 49. array_is_list() — finally answer "is this sequential?"
Determines if an array is a list (indexed from 0 without gaps).
```php
array_is_list(['a', 'b', 'c']); // true
```

<a name="ffi"></a>

### 50. FFI — call C libraries directly from PHP
Foreign Function Interface (PHP 7.4) lets you call C functions without building extensions.
```php
$ffi = FFI::cdef("double cos(double x);", "libm.so.6");
echo $ffi->cos(M_PI);
```

<a name="weakmap"></a>

### 52. WeakMap (PHP 8.0)
Maps with objects as keys that don't prevent garbage collection, perfect for per-object caches.
```php
$cache = new WeakMap();
$cache[$obj] = $computedData;
```

<a name="named-capture-groups"></a>

### 53. Named capture groups in preg_match
Regex captures can now be accessed by name instead of just numeric indices.
```php
preg_match('/(?P<year>\d{4})/', '2024', $matches);
echo $matches['year'];
```

<a name="spaceship-operator"></a>

### 54. Spaceship operator `<=>` — three-way comparison
PHP 7.0 operator that returns -1, 0, or 1, perfect for sorting.
```php
usort($users, fn($a, $b) => $a->age <=> $b->age);
```

<a name="array-destructuring"></a>

### 55. Array destructuring with keys — pattern matching for arrays
Extract specific values from associative arrays easily.
```php
['name' => $name, 'age' => $age] = $person;
```

<a name="static-analysis"></a>

### 58. PHPStan & Psalm — static analysis as a first-class citizen
Tools that provide TypeScript-grade type safety with zero runtime overhead.

<a name="numeric-separators"></a>

### 59. Numeric literal separators (PHP 7.4)
Use underscores to make large numbers more readable.
```php
$population = 8_000_000_000;
```

<a name="nativephp"></a>

### 69. NativePHP — desktop and mobile apps, all in PHP
Build native cross-platform apps using the PHP you already know.

## Ecosystem and Performance

- **Composer:** Best-in-class dependency management.
- **OPcache & JIT:** Significant performance boosts.
- **Symfony & Laravel:** Mature, high-quality frameworks.
