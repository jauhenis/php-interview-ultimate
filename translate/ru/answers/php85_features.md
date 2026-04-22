# Новые возможности PHP 8.5

[Официальная документация: релиз PHP 8.5](https://www.php.net/releases/8.5/en.php)

## 1. Расширение URI

В PHP 8.5 представлено новое встроенное расширение для парсинга и манипулирования URI, поддерживающее стандарты RFC 3986 и WHATWG URL.

**Пример RFC 3986:**
```php
use Uri\Rfc3986\Uri;

$uri = new Uri('https://thephp.foundation:443/sponsor/');
echo $uri->getHost(); // thephp.foundation
echo $uri->getPort(); // 443
```

**Пример WHATWG:**
```php
use Uri\WhatWg\Url;

$url = new Url('https://example.com/path/../other');
echo $url->getPathname(); // /other
```

## 2. Оператор Pipe (`|>`)

Оператор pipe позволяет писать более читаемый код при цепочке вызовов функций. Результат выражения слева передается в качестве первого аргумента в вызываемый объект (callable) справа.

```php
$result = "  hello world  "
    |> trim(...)
    |> strtoupper(...)
    |> (fn($str) => str_replace('WORLD', 'PHP 8.5', $str)); 
// Результат: "HELLO PHP 8.5"
```

## 3. Clone With

Ключевое слово `clone` теперь поддерживает необязательный массив свойств, которые нужно обновить в процессе клонирования. Это особенно полезно для неизменяемых объектов (immutable) и `readonly` свойств.

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

## 4. Атрибут `#[\NoDiscard]`

Новый встроенный атрибут, который указывает на то, что возвращаемое значение функции или метода не должно игнорироваться.

```php
#[\NoDiscard]
function validateUser(): bool {
    // ...
    return true;
}

// Предупреждение: возвращаемое значение validateUser() игнорируется.
validateUser();

// Чтобы явно игнорировать, используйте приведение к (void):
(void) validateUser();
```

## 5. Замыкания и First-Class Callables в константных выражениях

Статические замыкания и first-class callables (например, `strlen(...)`) теперь можно использовать в константных выражениях, таких как параметры атрибутов и константы классов.

```php
class Formatter {
    public const string_formatter = strtoupper(...);
    
    #[MyAttribute(value: fn() => 'default')]
    public string $data;
}
```

## 6. Новые функции `array_first()` и `array_last()`

Нативные функции для получения первого и последнего значений массива. Возвращают `null`, если массив пуст.

```php
$arr = ['a' => 1, 'b' => 2, 'c' => 3];
$first = array_first($arr); // 1
$last = array_last($arr);   // 3
```

## 7. Улучшения атрибутов

- `#[\Override]` теперь может применяться к **свойствам**.
- `#[\Deprecated]` теперь можно использовать для **трейтов** и **констант**.
- Атрибуты теперь можно применять к **константам**.

## 8. Продвижение свойств в конструкторе: `final` свойства

Свойства, объявленные через конструктор, теперь могут быть помечены как `final`.

```php
class User {
    public function __construct(
        public final string $id,
        public string $name,
    ) {}
}
```

## 9. Важные изменения и устаревания

- Завершение операторов `case` точкой с запятой (используйте двоеточие `:`).
- Обратные кавычки (backticks) как псевдоним для `shell_exec()`.
- Магические методы `__sleep()` и `__wakeup()` (рекомендуется использовать `__serialize()` и `__unserialize()`).
- Нестандартные имена типов для приведения, такие как `(boolean)` и `(integer)` (используйте `(bool)` и `(int)`).
- PIE (PHP Installer for Extensions) теперь является рекомендованным инструментом вместо PECL.
