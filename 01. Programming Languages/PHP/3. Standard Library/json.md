# JSON

PHP предоставляет функции для работы с JSON (JavaScript Object Notation).

## Кодирование

```php
$data = ['name' => 'John', 'age' => 30];
$json = json_encode($data);
// {"name":"John","age":30}
```

### Опции кодирования

```php
// Сохранить Unicode как есть (не экранировать)
json_encode($data, JSON_UNESCAPED_UNICODE);

// Красивый вывод с отступами
json_encode($data, JSON_PRETTY_PRINT);

// Экранировать слеши
json_encode($data, JSON_UNESCAPED_SLASHES);

// Не экранировать амперсанды и угловые скобки
json_encode($data, JSON_HEX_AMP | JSON_HEX_TAG);

// Комбинация опций
json_encode($data, JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
```

## Декодирование

```php
$json = '{"name":"John","age":30}';
$data = json_decode($json);
// stdClass объект

$data = json_decode($json, true);
// Ассоциативный массив
```

### Обработка ошибок

```php
$json = 'invalid json';
$data = json_decode($json);

if (json_last_error() !== JSON_ERROR_NONE) {
    $error = json_last_error_msg();
    // Обработка ошибки
}
```

## Константы ошибок

- `JSON_ERROR_NONE` - нет ошибок
- `JSON_ERROR_DEPTH` - превышена максимальная глубина стека
- `JSON_ERROR_STATE_MISMATCH` - неверный или некорректный JSON
- `JSON_ERROR_CTRL_CHAR` - ошибка управляющего символа
- `JSON_ERROR_SYNTAX` - синтаксическая ошибка
- `JSON_ERROR_UTF8` - некорректные символы UTF-8

## Валидация (PHP 8.3+)

```php
json_validate($json); // true/false
```

## Примеры использования

```php
// Чтение из файла
$json = file_get_contents('data.json');
$data = json_decode($json, true);

// Запись в файл
$data = ['users' => [...]];
file_put_contents('data.json', json_encode($data, JSON_PRETTY_PRINT));

// API ответ
header('Content-Type: application/json');
echo json_encode($response, JSON_UNESCAPED_UNICODE);
```
