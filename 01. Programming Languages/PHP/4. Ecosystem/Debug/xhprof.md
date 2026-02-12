# Xhprof

Xhprof — расширение PHP для профилирования производительности приложений.

## Установка

```bash
pecl install xhprof
```

Или используйте форк для PHP 7+:

```bash
pecl install xhprof-beta
```

## Базовое использование

```php
<?php
// Включение профилирования
xhprof_enable(XHPROF_FLAGS_CPU | XHPROF_FLAGS_MEMORY);

// Код для профилирования
// ...

// Остановка и получение данных
$xhprof_data = xhprof_disable();
```

## Флаги

```php
<?php
// CPU профилирование
xhprof_enable(XHPROF_FLAGS_CPU);

// Профилирование памяти
xhprof_enable(XHPROF_FLAGS_MEMORY);

// CPU + Memory
xhprof_enable(XHPROF_FLAGS_CPU | XHPROF_FLAGS_MEMORY);

// С дополнительными опциями
xhprof_enable(
    XHPROF_FLAGS_CPU | XHPROF_FLAGS_MEMORY,
    ['ignored_functions' => ['call_user_func', 'call_user_func_array']]
);
```

## Отправка в Buggregator

```php
<?php
xhprof_enable(XHPROF_FLAGS_CPU | XHPROF_FLAGS_MEMORY);

// Код для профилирования
// ...

$xhprof_data = xhprof_disable();

// Отправка в Buggregator
$requestData = [
    'profile' => $xhprof_data,
    'tags' => '',
    'app_name' => 'Test app',
    'hostname' => gethostname(),
    'date' => (new DateTime())->getTimestamp(),
];

$ch = curl_init('http://profiler@buggregator:8000');
$payload = json_encode($requestData);
curl_setopt($ch, CURLOPT_POSTFIELDS, $payload);
curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$result = curl_exec($ch);
curl_close($ch);
```

## GUI для просмотра

### XHGui

Веб-интерфейс для просмотра профилей Xhprof.

```bash
composer require --dev perftools/xhgui-collector
```

- https://github.com/perftools/xhgui

## Сравнение с Xdebug Profiler

- **Xhprof** — легче, меньше накладных расходов, подходит для production
- **Xdebug** — больше функций, но больше влияет на производительность

## Ссылки

- https://pecl.php.net/package/xhprof
- https://github.com/longxinH/xhprof
- https://github.com/perftools/xhgui
