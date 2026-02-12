# New Relic

New Relic — платформа для мониторинга производительности приложений, включая PHP приложения.

## Установка

### Через PECL

```bash
pecl install newrelic
```

### Через пакетный менеджер

```bash
# Ubuntu/Debian
apt-get install newrelic-php5

# CentOS/RHEL
yum install newrelic-php5
```

## Конфигурация

### php.ini

```ini
[newrelic]
newrelic.license = "YOUR_LICENSE_KEY"
newrelic.appname = "My Application"
newrelic.enabled = true
```

### Переменные окружения

```bash
NEW_RELIC_LICENSE_KEY=your_key
NEW_RELIC_APP_NAME="My App"
```

## Использование в коде

### Транзакции

```php
<?php
// Начать транзакцию
newrelic_start_transaction();

// Завершить транзакцию
newrelic_end_transaction();
```

### Кастомные метрики

```php
<?php
// Добавить метрику
newrelic_custom_metric('Custom/Metric', 123.45);

// Увеличить метрику
newrelic_custom_metric('Custom/Counter', 1);
```

### Параметры транзакции

```php
<?php
// Добавить атрибут к транзакции
newrelic_add_custom_parameter('user_id', 123);
newrelic_add_custom_parameter('action', 'login');

// Установить имя транзакции
newrelic_name_transaction('CustomTransactionName');
```

### Игнорирование транзакций

```php
<?php
// Игнорировать текущую транзакцию
newrelic_ignore_transaction();

// Игнорировать апдейты
newrelic_ignore_apdex();
```

### Ошибки и исключения

```php
<?php
try {
    // код
} catch (Exception $e) {
    newrelic_notice_error($e);
    // или
    newrelic_notice_error($e->getMessage(), $e);
}
```

### Запись событий

```php
<?php
newrelic_record_custom_event('MyEvent', [
    'key1' => 'value1',
    'key2' => 'value2'
]);
```

## Интеграция с фреймворками

### Symfony

```yaml
# config/packages/newrelic.yaml
newrelic:
    enabled: true
    license_key: '%env(NEW_RELIC_LICENSE_KEY)%'
    app_name: '%env(NEW_RELIC_APP_NAME)%'
```

### Laravel

```php
// config/newrelic.php
return [
    'license' => env('NEW_RELIC_LICENSE_KEY'),
    'app_name' => env('NEW_RELIC_APP_NAME'),
];
```

## Мониторинг производительности

- **APM (Application Performance Monitoring)** — мониторинг производительности приложения
- **Browser Monitoring** — мониторинг производительности браузера
- **Infrastructure Monitoring** — мониторинг инфраструктуры
- **Synthetics** — мониторинг доступности

## Ссылки

- https://docs.newrelic.com/docs/agents/php-agent/
- https://github.com/newrelic/newrelic-php-agent
