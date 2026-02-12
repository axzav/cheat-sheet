# Конфигурация PHP

Конфигурация PHP управляется через файл `php.ini` и функции `ini_set()` / `ini_get()`.

## Основные директивы

### Отображение ошибок

```ini
display_errors = On          ; Отображать ошибки
display_startup_errors = On  ; Отображать ошибки запуска
error_reporting = E_ALL      ; Уровень протоколирования
log_errors = On              ; Логировать ошибки
error_log = /path/to/error.log ; Путь к логу ошибок
```

### Память и время выполнения

```ini
memory_limit = 128M          ; Лимит памяти
max_execution_time = 30      ; Максимальное время выполнения (секунды)
max_input_time = 60          ; Максимальное время обработки входных данных
post_max_size = 8M           ; Максимальный размер POST данных
upload_max_filesize = 2M     ; Максимальный размер загружаемого файла
```

### Сессии

```ini
session.save_handler = files  ; Обработчик хранения сессий
session.save_path = /tmp     ; Путь для хранения сессий
session.gc_maxlifetime = 1440 ; Время жизни сессии (секунды)
session.cookie_lifetime = 0   ; Время жизни cookie сессии
session.cookie_secure = 1    ; Только HTTPS
session.cookie_httponly = 1  ; HttpOnly флаг
```

### Дата и время

```ini
date.timezone = Europe/Moscow ; Часовой пояс по умолчанию
```

### Кодировка

```ini
default_charset = "UTF-8"    ; Кодировка по умолчанию
```

### Безопасность

```ini
allow_url_fopen = On         ; Разрешить открытие URL как файлов
allow_url_include = Off       ; Разрешить include из URL (опасно!)
expose_php = Off             ; Не показывать версию PHP в заголовках
```

## Работа с конфигурацией в коде

### Получение значения

```php
$value = ini_get('memory_limit');
$all = ini_get_all(); // Все директивы
$all = ini_get_all('pcre'); // Директивы конкретного модуля
```

### Установка значения

```php
ini_set('memory_limit', '256M');
ini_set('display_errors', '1');
```

### Восстановление значения

```php
ini_restore('memory_limit');
```

### Проверка изменяемости

```php
if (ini_get('memory_limit') !== false) {
    // Директива существует
}

// Проверить, можно ли изменить
$changeable = ini_get_all()['memory_limit']['access'] === 7;
```

## Файлы конфигурации

### Расположение php.ini

```php
php_ini_loaded_file();     // Путь к загруженному php.ini
php_ini_scanned_files();    // Дополнительные ini файлы
```

### Загрузка дополнительных ini файлов

```bash
php -c /path/to/custom.ini script.php
```

## Режимы конфигурации

### CLI vs Web

PHP может иметь разные конфигурации для CLI и веб-сервера:

- CLI: `/etc/php/8.x/cli/php.ini`
- Web: `/etc/php/8.x/fpm/php.ini` или `/etc/php/8.x/apache2/php.ini`

### Проверка режима

```php
if (php_sapi_name() === 'cli') {
    // CLI режим
} else {
    // Web режим
}
```

## Переопределение через .htaccess (Apache)

```apache
php_value memory_limit 256M
php_flag display_errors On
php_value error_reporting E_ALL
```

## Лучшие практики

1. **Не изменяйте php.ini напрямую** - используйте `ini_set()` в коде или конфигурацию веб-сервера
2. **Отключайте display_errors в production** - используйте логирование
3. **Устанавливайте разумные лимиты** - memory_limit, max_execution_time
4. **Используйте правильный часовой пояс** - date.timezone
5. **Настройте сессии правильно** - безопасные cookie, правильное хранилище
