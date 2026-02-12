# Errors

## Типы ошибок

PHP различает несколько типов ошибок:

- **E_ERROR** — фатальные ошибки времени выполнения
- **E_WARNING** — предупреждения (не останавливают выполнение)
- **E_PARSE** — ошибки парсинга (останавливают выполнение до интерпретации)
- **E_NOTICE** — уведомления о потенциальных проблемах
- **E_STRICT** — рекомендации по совместимости
- **E_DEPRECATED** — устаревшие функции
- **E_ALL** — все ошибки

## Настройка протоколирования

### error_reporting

Уровень протоколирования ошибок настраивается через `error_reporting()` или в `php.ini`:

```php
// Отключить все ошибки
error_reporting(0);

// Простые ошибки выполнения
error_reporting(E_ERROR | E_WARNING | E_PARSE);

// Все ошибки кроме E_NOTICE
error_reporting(E_ALL & ~E_NOTICE);

// Все ошибки
error_reporting(E_ALL);
error_reporting(-1); // Эквивалентно E_ALL

// Получить текущее значение
var_dump(error_reporting());

// Через ini_set
ini_set('error_reporting', E_ALL);
```

**Важно:** `error_reporting()` не влияет на `E_PARSE`, так как парсинг происходит до интерпретации кода.

### display_errors

Управляет отображением ошибок в stdout:

```ini
display_errors = On   ; Отображать ошибки
display_errors = Off  ; Не отображать
display_errors = stderr ; В stderr (PHP 8.0+)
```

**Важно:** Даже при `display_errors=Off` и `error_reporting=0`, при фатальной ошибке nginx вернет 500, так как php-fpm не отдаст заголовки.
