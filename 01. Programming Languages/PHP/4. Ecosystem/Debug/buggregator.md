# Buggregator

Buggregator — инструмент для сбора и просмотра отладочной информации (dumps, logs, profiles) в одном месте.

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

См. пример в `xhprof.md` для отправки профилей в Buggregator.

## Интеграция с Xdebug

```env
XDEBUG_CONFIG="profiler_output_dir=/tmp/xdebug"
```

Профили Xdebug можно отправлять в Buggregator через API.

## Просмотр данных

Buggregator собирает:

- **Dumps** — из Symfony VarDumper
- **Logs** — из Monolog и других логгеров
- **Profiles** — из Xhprof, Xdebug и других профилировщиков
- **Traces** — трассировки выполнения

## Фильтрация и поиск

В веб-интерфейсе доступны:

- Фильтрация по типу данных
- Поиск по содержимому
- Группировка по запросам
- Временная шкала событий

## Ссылки

- https://buggregator.dev/
- https://docs.buggregator.dev/getting-started.html
- https://docs.buggregator.dev/config/inspector.html
