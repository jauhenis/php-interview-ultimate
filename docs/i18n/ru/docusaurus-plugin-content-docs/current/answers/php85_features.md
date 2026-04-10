---
title: 'Новые возможности PHP 8.5'
slug: '/answers/php85_features'
---

# Новые возможности PHP 8.5

[Официальная документация: релиз PHP 8.5](https://www.php.net/releases/8.5/en.php)

## 1. Расширение URI

PHP 8.5 вводит новое встроенное расширение для парсинга и манипуляции URI. Оно заменяет множество ручных вызовов `parse_url` и кастомный парсинг URI на основе регулярных выражений более надёжным объектно-ориентированным подходом.

```php
use URI\URI;

$uri = URI::parse("https://user:pass@example.com:8080/path?query=1#hash");
echo $uri->getHost(); // example.com
echo $uri->getPort(); // 8080
```

## 2. Оператор pipe (`|>`)

Оператор pipe обеспечивает более читаемый синтаксис при цепочке вызовов функций. Результат левого выражения передаётся в качестве первого аргумента в вызов функции справа.

```php
$result = "  hello world  "
    |> 'trim'
    |> 'strtoupper'
    |> 'str_replace'('WORLD', 'PHP 8.5', ...); // "HELLO PHP 8.5"
```

## 3. Атрибут `#[\NoDiscard]`

Новый встроенный атрибут, который сигнализирует инструментам статического анализа и IDE о том, что возвращаемое значение функции или метода не должно игнорироваться.

```php
#[\NoDiscard]
function validateUser(): bool {
    // ...
    return true;
}

// Warning: Return value of validateUser() is ignored.
validateUser();
```

## 4. Замыкания и First-Class Callable в константных выражениях

Замыкания и first-class callable (например, `strlen(...)`) теперь можно использовать в константных выражениях: в объявлениях `const`, константах классов и значениях свойств по умолчанию.

```php
class Formatter {
    public const string_formatter = strtoupper(...);
    private Closure $validator = fn($v) => !empty($v);
}
```

## 5. Новые функции `array_first()` и `array_last()`

В PHP 8.5 наконец появились нативные функции для получения первого и последнего элементов массива без перемещения внутреннего указателя массива.

```php
$arr = ['a' => 1, 'b' => 2, 'c' => 3];
$first = array_first($arr); // 1
$last = array_last($arr);   // 3
```

## 6. cURL: новая функция `curl_multi_get_handles()`

Новая функция расширения cURL, позволяющая получить все дескрипторы, связанные с мульти-дескриптором.

```php
$multiHandle = curl_multi_init();
// ... add some handles ...
$handles = curl_multi_get_handles($multiHandle);
```

## 7. Filter: исключения при ошибках валидации

Функции `filter_var` и связанные с ними теперь поддерживают выброс исключений при ошибках валидации с помощью нового флага.

```php
try {
    filter_var("invalid-email", FILTER_VALIDATE_EMAIL, FILTER_THROW_ON_FAILURE);
} catch (FilterException $e) {
    echo $e->getMessage();
}
```

## 8. Новая INI-директива `max_memory_limit`

Эта директива позволяет задать верхний предел для `memory_limit`, который не может быть превышен вызовами `ini_set()` во время выполнения.

## 9. PIE (PHP Installer for Extensions)

PHP 8.5 знаменует официальный переход от устаревшего **PECL** к **PIE**. PIE — это современный установщик PHP-расширений на основе PHAR, работающий аналогично Composer.

- **GitHub-репозиторий:** [https://github.com/php/pie](https://github.com/php/pie)
- **Использование:** `pie install <extension-name>`
