# PHP 8.2 New Features

[Official Documentation: PHP 8.2 Release](https://www.php.net/releases/8.2/en.php)

## 1. Readonly Classes
Marks an entire class as readonly, making all its properties readonly automatically.
```php
readonly class User {
    public function __construct(
        public string $name,
        public string $email,
    ) {}
}
```

## 2. Disjunctive Normal Form (DNF) Types
Allows combining union and intersection types. Intersection types must be grouped with parentheses.
```php
public function process((A&B)|null $entity) { ... }
```

## 3. `null`, `false`, and `true` as Standalone Types
These can now be used as return types on their own.
```php
public function alwaysFalse(): false { return false; }
```

## 4. New "Random" Extension
A new object-oriented API for random number generation, replacing the globally seeded RNG.
```php
use Random\Randomizer;
$randomizer = new Randomizer();
echo $randomizer->getInt(1, 100);
```

## 5. Constants in Traits
Traits can now define constants. These constants can be accessed through the class that uses the trait.
```php
trait Foo {
    public const BAR = 'baz';
}
class MyClass {
    use Foo;
}
echo MyClass::BAR;
```

## 6. Deprecate Dynamic Properties
Creating properties that were not declared in the class is now deprecated, unless the class opts in with the `#[AllowDynamicProperties]` attribute. `stdClass` is unaffected.

## 7. New Functions and Classes
- `mysqli_execute_query()`
- `#[SensitiveParameter]` attribute to hide sensitive data in stack traces.
- `memory_reset_peak_usage()`
