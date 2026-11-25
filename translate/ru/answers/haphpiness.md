# HaPHPiness - Лучшее в PHP

Этот гайд вдохновлен ресурсом [haphpiness.com](https://haphpiness.com/) и подчеркивает современные возможности, которые делают PHP отличным языком для разработки.

## Современные возможности языка

### 1. Named Arguments / Именованные аргументы (PHP 8.0)
Именованные аргументы позволяют передавать значения в функцию, указывая имя параметра. Это избавляет от необходимости следить за порядком аргументов.
```php
// После: именованные аргументы
htmlspecialchars($string, double_encode: false);
```

### 2. Union Types (PHP 8.0) и Intersection Types (PHP 8.1)
Union Types (объединения) позволяют переменным иметь несколько типов. Intersection Types (пересечения) требуют, чтобы значение удовлетворяло сразу нескольким типам.
```php
// Union types
function process(int|string $input): void {}

// Intersection types
function save(Countable&Iterator $collection): void {}
```

### 3. Enums / Перечисления (PHP 8.1)
Полноценная поддержка перечислений, обеспечивающая типобезопасность для фиксированных наборов значений.
```php
enum Status: string {
    case Active = 'active';
    case Inactive = 'inactive';
}
```

<a name="string-functions"></a>

### 4. str_contains(), str_starts_with(), str_ends_with() (PHP 8.0)
Удобные и читаемые функции для стандартных операций со строками.
```php
if (str_contains($url, 'https')) { ... }
if (str_starts_with($file, '/var/www')) { ... }
if (str_ends_with($file, '.php')) { ... }
```

<a name="array-unpacking"></a>

### 5. Array Unpacking with String Keys / Распаковка массивов со строковыми ключами (PHP 8.1)
Оператор расширения (`...`) теперь работает со строковыми ключами, что делает слияние массивов лаконичным.
```php
$config = [...$defaults, ...$custom];
```

### 6. Fibers / Файберы (PHP 8.1)
Легковесные корутины для реализации асинхронных примитивов без "callback hell".
```php
$fiber = new Fiber(function (): void {
    $value = Fiber::suspend('paused');
    echo "Resumed with: $value";
});
```

<a name="dev-server"></a>

### 8. Встроенный сервер разработки (php -S)
Для локальной разработки не требуется настройка Apache или Nginx. Просто запустите:
```bash
php -S localhost:8000
```

<a name="arrow-functions"></a>

### 10. Arrow Functions / Стрелочные функции (PHP 7.4)
Краткий синтаксис для коротких замыканий с автоматическим захватом переменных из родительской области видимости.
```php
$doubled = array_map(fn($n) => $n * 2, $numbers);
```

<a name="match-expressions"></a>

### 11. Match Expressions / Выражения Match (PHP 8.0)
Более мощная и безопасная альтернатива `switch`, использующая строгое сравнение и возвращающая значение.
```php
$text = match($statusCode) {
    200, 201 => 'Success',
    404      => 'Not Found',
    default  => 'Unknown',
};
```

<a name="null-coalescing"></a>

### 12. Null Coalescing Operator ?? и ??= / Оператор объединения с null (PHP 7.0+)
Удобный способ обработки значений по умолчанию и ленивой инициализации.
```php
$username = $_GET['user'] ?? 'anonymous';
$this->logger ??= new NullLogger();
```

### 15. Readonly Properties and Classes / Readonly свойства и классы (PHP 8.1+)
Иммутабельность на уровне движка для свойств и целых классов.
```php
readonly class Money {
    public function __construct(public int $amount, public string $currency) {}
}
```

<a name="attributes"></a>

### 17. Атрибуты — нативные метаданные
PHP 8.0 заменил аннотации в комментариях (docblocks) реальным синтаксисом, который проверяется инструментами статического анализа.
```php
#[Route('/users', method: 'GET')]
public function index(): Response { ... }
```

### 34. Property Hooks / Хуки свойств (PHP 8.4)
Определение логики `get` и `set` прямо при объявлении свойства, что избавляет от шаблонного кода геттеров и сеттеров.
```php
class User {
    public string $fullName {
        get => $this->firstName . ' ' . $this->lastName;
    }
}
```

### 39. Pipe Operator `|>` / Конвейерный оператор (PHP 8.5)
Поток данных слева направо для цепочек вызовов функций.
```php
$slug = $title
    |> trim(...)
    |> strtolower(...);
```

<a name="nodiscard"></a>

### 43. #[\NoDiscard] (PHP 8.5)
Предупреждает, если возвращаемое значение функции игнорируется. Важно для результатов валидации или иммутабельных операций.
```php
#[\NoDiscard]
function validate(array $data): bool { ... }
```

<a name="fatal-error-backtraces"></a>

### 44. Fatal error backtraces / Трассировка стека при фатальных ошибках
В PHP 8.5 фатальные ошибки (например, превышение времени выполнения) включают трассировку стека, что значительно упрощает отладку на проде.

<a name="uri-extension"></a>

### 45. URI Extension / Расширение URI
PHP 8.5 вводит встроенное расширение для работы с URI, соответствующее стандартам, с иммутабельными объектами URL.
```php
use Uri\Rfc3986\Uri;
$uri = new Uri('https://example.com/path');
```

<a name="array-is-list"></a>

### 49. array_is_list() — окончательный ответ на вопрос "является ли массив списком?"
Определяет, является ли массив списком (индексированным от 0 без пропусков).
```php
array_is_list(['a', 'b', 'c']); // true
```

<a name="ffi"></a>

### 50. FFI (Foreign Function Interface) — прямой вызов C-библиотек
Интерфейс внешних функций (PHP 7.4) позволяет вызывать функции C без написания расширений на C.
```php
$ffi = FFI::cdef("double cos(double x);", "libm.so.6");
echo $ffi->cos(M_PI);
```

<a name="weakmap"></a>

### 52. WeakMap (PHP 8.0)
Карты, где ключами являются объекты, которые не препятствуют сборке мусора. Идеально для кэширования данных об объектах.
```php
$cache = new WeakMap();
$cache[$obj] = $computedData;
```

<a name="named-capture-groups"></a>

### 53. Named capture groups / Именованные группы захвата в preg_match
Результаты регулярных выражений теперь можно получать по имени, а не только по числовому индексу.
```php
preg_match('/(?P<year>\d{4})/', '2024', $matches);
echo $matches['year'];
```

<a name="spaceship-operator"></a>

### 54. Spaceship operator `<=>` / Оператор «космический корабль»
Оператор PHP 7.0, который возвращает -1, 0 или 1, идеально подходит для сортировки.
```php
usort($users, fn($a, $b) => $a->age <=> $b->age);
```

<a name="array-destructuring"></a>

### 55. Array destructuring with keys / Распаковка массива с ключами
Удобное извлечение конкретных значений из ассоциативных массивов.
```php
['name' => $name, 'age' => $age] = $person;
```

<a name="static-analysis"></a>

### 58. PHPStan и Psalm — статический анализ как стандарт
Инструменты, обеспечивающие типобезопасность уровня TypeScript без накладных расходов во время выполнения.

<a name="numeric-separators"></a>

### 59. Numeric Literal Separators / Разделители в числах (PHP 7.4)
Использование нижнего подчеркивания для улучшения читаемости больших чисел.
```php
$population = 8_000_000_000;
```

<a name="nativephp"></a>

### 69. NativePHP — десктопные и мобильные приложения на PHP
Создавайте нативные кроссплатформенные приложения, используя уже знакомый PHP.

## Экосистема и производительность

- **Composer:** Лучший менеджер зависимостей в своем классе.
- **OPcache & JIT:** Значительный прирост производительности.
- **Symfony & Laravel:** Зрелые и качественные фреймворки.
