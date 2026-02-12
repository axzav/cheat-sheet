# Monolog

Monolog — популярная библиотека для логирования в PHP, совместимая с PSR-3.

## Установка

```bash
composer require monolog/monolog
```

## Базовое использование

```php
<?php
use Monolog\Logger;
use Monolog\Handler\StreamHandler;

// Создание логгера
$logger = new Logger('name');
$logger->pushHandler(new StreamHandler('path/to/your.log', Logger::WARNING));

// Логирование
$logger->warning('Foo');
$logger->error('Bar');
```

## Уровни логирования

```php
<?php
$logger->debug('Debug information');
$logger->info('Informational message');
$logger->notice('Normal but significant condition');
$logger->warning('Warning message');
$logger->error('Error message');
$logger->critical('Critical condition');
$logger->alert('Action must be taken immediately');
$logger->emergency('System is unusable');
```

## Handlers

### StreamHandler

```php
<?php
use Monolog\Handler\StreamHandler;

// Логирование в файл
$logger->pushHandler(new StreamHandler('app.log', Logger::DEBUG));

// Логирование в stdout
$logger->pushHandler(new StreamHandler('php://stdout', Logger::DEBUG));
```

### RotatingFileHandler

```php
<?php
use Monolog\Handler\RotatingFileHandler;

// Ротация логов по дням
$logger->pushHandler(new RotatingFileHandler('app.log', 0, Logger::DEBUG));
```

### SyslogHandler

```php
<?php
use Monolog\Handler\SyslogHandler;

$logger->pushHandler(new SyslogHandler('my_app', LOG_USER, Logger::DEBUG));
```

### ErrorLogHandler

```php
<?php
use Monolog\Handler\ErrorLogHandler;

$logger->pushHandler(new ErrorLogHandler(ErrorLogHandler::OPERATING_SYSTEM, Logger::DEBUG));
```

### SlackHandler

```php
<?php
use Monolog\Handler\SlackHandler;

$logger->pushHandler(new SlackHandler(
    'webhook-url',
    '#channel',
    'Monolog',
    true,
    null,
    Logger::ERROR
));
```

### TelegramBotHandler

```php
<?php
use Monolog\Handler\TelegramBotHandler;

$logger->pushHandler(new TelegramBotHandler(
    'token',
    'chat_id',
    Logger::ERROR
));
```

## Formatters

### LineFormatter

```php
<?php
use Monolog\Formatter\LineFormatter;

$formatter = new LineFormatter(
    "[%datetime%] %channel%.%level_name%: %message% %context% %extra%\n",
    'Y-m-d H:i:s'
);

$handler = new StreamHandler('app.log', Logger::DEBUG);
$handler->setFormatter($formatter);
$logger->pushHandler($handler);
```

### JsonFormatter

```php
<?php
use Monolog\Formatter\JsonFormatter;

$handler = new StreamHandler('app.log', Logger::DEBUG);
$handler->setFormatter(new JsonFormatter());
$logger->pushHandler($handler);
```

## Processors

### Добавление контекста

```php
<?php
use Monolog\Processor\MemoryUsageProcessor;
use Monolog\Processor\UidProcessor;
use Monolog\Processor\WebProcessor;

$logger->pushProcessor(new MemoryUsageProcessor());
$logger->pushProcessor(new UidProcessor());
$logger->pushProcessor(new WebProcessor());
```

### Кастомный процессор

```php
<?php
use Monolog\LogRecord;

$logger->pushProcessor(function (LogRecord $record) {
    $record->extra['user_id'] = get_current_user_id();
    return $record;
});
```

## Контекст и дополнительные данные

```php
<?php
// Добавление контекста
$logger->info('User logged in', [
    'user_id' => 123,
    'ip' => '192.168.1.1'
]);

// Добавление исключения
try {
    // код
} catch (\Exception $e) {
    $logger->error('Error occurred', [
        'exception' => $e,
        'context' => ['key' => 'value']
    ]);
}
```

## Каналы (Channels)

```php
<?php
$dbLogger = new Logger('database');
$dbLogger->pushHandler(new StreamHandler('db.log', Logger::DEBUG));

$apiLogger = new Logger('api');
$apiLogger->pushHandler(new StreamHandler('api.log', Logger::DEBUG));
```

## Интеграция с фреймворками

### Symfony

```yaml
# config/packages/monolog.yaml
monolog:
    handlers:
        main:
            type: stream
            path: "%kernel.logs_dir%/%kernel.environment%.log"
            level: debug
            channels: ["!event"]
```

### Laravel

```php
// config/logging.php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily'],
    ],
    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
        'days' => 14,
    ],
],
```

## Ссылки

- https://github.com/Seldaek/monolog
- https://seldaek.github.io/monolog/
- https://betterstack.com/community/guides/logging/how-to-start-logging-with-monolog/
