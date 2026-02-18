# Buggregator

Buggregator — инструмент для сбора и просмотра отладочной информации в одном месте

Buggregator собирает:
- **Dumps** — из Symfony VarDumper
- **Logs** — из Monolog и других логгеров
- **Profiles** — из Xhprof, Xdebug и других профилировщиков
- **Traces** — трассировки выполнения

## Установка

### Docker

```bash
docker run -d \
  --name buggregator \
  -p 8000:8000 \
  -p 9912:9912 \
  -p 9913:9913 \
  ghcr.io/buggregator/server:latest
```

### Docker Compose

```yaml
version: '3.8'
services:
  buggregator:
    image: ghcr.io/buggregator/server:latest
    ports:
      - "8000:8000"
      - "9912:9912"
      - "9913:9913"
```

## Веб-интерфейс

После запуска доступен по адресу:

```
http://127.0.0.1:8000/#/
```

## Интеграция с Symfony VarDumper

### Установка

```bash
composer require --dev symfony/var-dumper
```

### Конфигурация (.env)

```env
VAR_DUMPER_FORMAT=server
VAR_DUMPER_SERVER="buggregator:9912"
```

### Использование

```php
<?php
// Все dump() будут отправляться в Buggregator
dump($variable);
```

## Интеграция с Monolog

### Конфигурация (.env)

```env
MONOLOG_SOCKET_HOST="buggregator:9913"
```

### Настройка Monolog

```php
<?php
use Monolog\Handler\SocketHandler;

$handler = new SocketHandler('buggregator:9913');
$logger->pushHandler($handler);
```

## Интеграция с Xhprof

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

## Интеграция с Xdebug

```env
XDEBUG_CONFIG="profiler_output_dir=/tmp/xdebug"
```

Профили Xdebug можно отправлять в Buggregator через API.


## Ссылки

- https://buggregator.dev/
- https://docs.buggregator.dev/getting-started.html
- https://docs.buggregator.dev/config/inspector.html
