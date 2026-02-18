# Xdebug

Xdebug — расширение PHP для отладки и профилирования кода. Предоставляет улучшенные stack traces, пошаговую отладку, профилирование производительности и трассировку выполнения.

- **Stack traces**: Улучшенные трассировки стека с типами и значениями параметров
- **Breakpoints**: Точки останова для остановки выполнения
- **Step Debugging**: Пошаговая отладка с контролем выполнения
- **Profiler**: Профилирование производительности кода
- **Tracing**: Трассировка всех вызовов функций
- **Code Coverage**: Покрытие кода тестами (для PHPUnit)

---

## Установка

### Проверка установки

```sh
php -v
# Должна быть строка: with Xdebug v3.x.x
```

### Установка через PECL

```sh
pecl install xdebug
```

### Установка в Docker

```dockerfile
FROM php:7.4-apache
RUN pecl install xdebug && docker-php-ext-enable xdebug
COPY ./php.ini /usr/local/etc/php/
```

### Проверка конфигурации

```shell
php -r "xdebug_info();"
```

```php
xdebug_info(); // Выводит информацию о конфигурации и триггерах
```

---

## Конфигурация

### Основные параметры

```ini
[xdebug]
zend_extension=xdebug.so

# Режимы работы (через запятую)
xdebug.mode=develop,debug,profile,trace

# Активация при запросе
xdebug.start_with_request=trigger

# Подключение к IDE
xdebug.client_host=host.docker.internal
xdebug.client_port=9003
xdebug.idekey=PHPSTORM

# Логирование
xdebug.log=/var/log/xdebug.log

# Директории для вывода
xdebug.trace_output_dir=/var/www/traces
xdebug.output_dir=/var/www/profiles

# Дополнительные настройки
xdebug.max_nesting_level=1000
```

### Переопределение через переменные окружения

Параметры можно переопределять через переменные окружения (удобно в Docker):

```yaml
services:
  app:
    environment:
      XDEBUG_CONFIG: 'client_host=host.docker.internal client_port=9003'
      PHP_IDE_CONFIG: 'serverName=api-core-docker'
      XDEBUG_MODE: 'debug,profile'
```

**Важно:**
- `XDEBUG_MODE` устанавливается до запуска PHP и не может быть изменен через GET/POST/COOKIE
- `XDEBUG_CONFIG` переопределяет параметры из `php.ini`, но не `xdebug.mode`
- `PHP_IDE_CONFIG` указывает имя сервера в IDE (должно совпадать с настройками IDE)

---

## Режимы работы (Modes)

Параметр `xdebug.mode` настраивается только в `php.ini` и не может быть переопределен через `XDEBUG_CONFIG`. Все указанные режимы работают одновременно.

### Доступные режимы

- **off** — Xdebug полностью отключен
- **develop** (по умолчанию) — улучшенные `var_dump`, stack traces
- **debug** — отладка через IDE (breakpoints, step debugging)
- **profile** — профилирование производительности (cachegrind файлы)
- **trace** — трассировка всех вызовов функций с параметрами
- **coverage** — покрытие кода тестами (для PHPUnit)
- **gcstats** — статистика сборщика мусора

### Пример конфигурации

```ini
xdebug.mode=develop,debug,profile,trace
```

Включать только необходимые режимы, так как они влияют на производительность.

---

## Активация через триггеры

### Параметр `xdebug.start_with_request`

- **yes** — активируется при каждом запросе
- **trigger** — активируется только при наличии триггера (рекомендуется)
- **default** — активируется при `XDEBUG_SESSION` или команде профилирования/трассировки
- **no** — никогда не активируется автоматически

### Триггеры для HTTP-запросов

GET/POST параметры или COOKIE:

- `XDEBUG_SESSION_START=PHPSTORM` — для отладки (debug mode)
- `XDEBUG_TRIGGER=1` — универсальный триггер для всех режимов
- `XDEBUG_PROFILE=1` — для профилирования
- `XDEBUG_TRACE=1` — для трассировки

**Примеры:**

```
http://localhost/myapp?XDEBUG_SESSION_START=PHPSTORM
http://localhost/myapp?XDEBUG_TRIGGER=1
http://localhost/myapp?XDEBUG_TRIGGER=1&XDEBUG_TRACE=1
```

В Postman можно добавить cookie: `XDEBUG_SESSION=PHPSTORM`

### Триггеры для CLI

```sh
XDEBUG_TRIGGER=1 php bin/console app:test-command
XDEBUG_SESSION_START=1 php bin/phpunit tests/
```

---

## Отладка (Debug Mode)

### Настройка для Docker

```ini
[xdebug]
xdebug.mode=debug
xdebug.client_host=host.docker.internal
xdebug.client_port=9003
xdebug.start_with_request=trigger
xdebug.idekey=PHPSTORM
```

### Настройка PhpStorm

1. **File > Settings > Languages & Frameworks > PHP > Servers**
   - Добавить сервер с именем, совпадающим с `PHP_IDE_CONFIG` (например, `docker`)
   - Host: `localhost`, Port: `80`
   - Настроить path mappings (локальные пути → пути в контейнере)

2. **File > Settings > Languages & Frameworks > PHP > Debug**
   - Убедиться, что порт Xdebug: `9003`

3. Включить "Listen to Xdebug connections" (кнопка с телефоном)

### Использование

1. Установить breakpoints в коде
2. Включить "Listen to Xdebug connections" в IDE
3. Отправить запрос с триггером `XDEBUG_SESSION_START=PHPSTORM`
4. Выполнение остановится на breakpoint

### Принудительный breakpoint из кода

```php
xdebug_break(); // Остановка выполнения независимо от настроек
```

---

## Профилирование (Profile Mode)

Профилирование собирает статистику о времени выполнения и использовании памяти для каждой функции.

### Настройка

```ini
[xdebug]
xdebug.mode=profile
xdebug.start_with_request=trigger
xdebug.output_dir=/var/www/profiles
```

### Активация

HTTP:
```
http://localhost/myapp?XDEBUG_PROFILE=1
# или
http://localhost/myapp?XDEBUG_TRIGGER=1
```

CLI:
```sh
XDEBUG_TRIGGER=1 php bin/console your:command
```

### Результаты

Создаются файлы `cachegrind.out.*.gz` в директории `xdebug.output_dir`.

### Анализ профилей

- **QCacheGrind** (macOS, Windows) — https://github.com/QCacheGrind/QCacheGrind
- **KCacheGrind** (Linux) — встроен в большинство дистрибутивов
- **Webgrind** — веб-интерфейс для анализа

Файлы нужно распаковать из `.gz` перед анализом.

---

## Трассировка (Trace Mode)

Трассировка записывает каждый вызов функции с аргументами, возвращаемыми значениями и временем выполнения.

### Настройка

```ini
[xdebug]
xdebug.mode=trace
xdebug.start_with_request=trigger
xdebug.trace_output_dir=/var/www/traces
xdebug.trace_format=0  # 0=human readable, 1=HTML, 2=computer format
```

### Программная активация

```php
// Начало трассировки
xdebug_start_trace('/tmp/my_trace.xt');

$res = $this->clientRepository->getClientsByAccount($accountId);

// Конец трассировки
xdebug_stop_trace();
```

### Активация через триггер

HTTP:
```
http://localhost/myapp?XDEBUG_TRACE=1
# или
http://localhost/myapp?XDEBUG_TRIGGER=1
```

### Результаты

Создаются файлы `trace.*.xt.gz` в директории `xdebug.trace_output_dir`.

### Анализ трассировок

- Текстовые редакторы (VS Code, Sublime Text и т.д.) — файлы `.xt` это текстовые файлы
- **Trace viewers:**
  - https://github.com/kuun/xdebug-trace-viewer
  - https://github.com/vtk13/xdebug-trace-viewer

Файлы нужно распаковать из `.gz` перед просмотром.

### Полная трассировка запроса

Для трассировки всего запроса от начала до конца:

```ini
xdebug.mode=trace
xdebug.start_with_request=yes  # вместо trigger
xdebug.trace_output_dir=/path/to/traces
```

**Внимание:** Трассировка генерирует большие файлы и замедляет выполнение.

---

## Различия: Профилирование vs Трассировка

**Профилирование (Profiling):**
- Собирает статистику: время выполнения, использование памяти
- Помогает найти узкие места (bottlenecks)
- Формат: cachegrind файлы
- Анализ: QCacheGrind, KCacheGrind

**Трассировка (Tracing):**
- Записывает порядок выполнения: каждый вызов функции, аргументы, возвращаемые значения
- Помогает понять логику выполнения и отладить сложные проблемы
- Формат: текстовые файлы .xt
- Анализ: текстовые редакторы, специализированные viewers

---

## Использование в Docker

### docker-compose.yml

```yaml
version: '3.8'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - .:/var/www/html
    ports:
      - 80:80
    environment:
      XDEBUG_CONFIG: 'client_host=host.docker.internal client_port=9003'
      PHP_IDE_CONFIG: 'serverName=docker'
      XDEBUG_MODE: 'debug,profile'
```

### Dockerfile

```dockerfile
FROM php:7.4-apache
RUN pecl install xdebug && docker-php-ext-enable xdebug
COPY ./php.ini /usr/local/etc/php/
```

### php.ini

```ini
[xdebug]
xdebug.mode=debug
xdebug.client_host=host.docker.internal
xdebug.client_port=9003
xdebug.start_with_request=trigger
xdebug.idekey=PHPSTORM
```

**Примечание:** `host.docker.internal` работает на Docker Desktop (Windows/Mac). На Linux IP-адрес хоста.

---

## Решение проблем

### Файлы трассировки не создаются

1. Проверьте права доступа на директорию `xdebug.trace_output_dir`
2. Убедитесь, что передаете правильные триггеры (`XDEBUG_TRACE=1` или `XDEBUG_TRIGGER=1`)
3. Проверьте логи Xdebug: `xdebug.log`
4. Убедитесь, что режим `trace` включен в `xdebug.mode`

### Сообщение "Trigger value for 'XDEBUG_TRIGGER' not found"

Xdebug ищет триггер, но не находит его. Убедитесь, что передаете `XDEBUG_TRIGGER=1` или `XDEBUG_TRACE=1` в запросе.

### Файлы профилирования/трассировки

- `cachegrind.out.*.gz` — файлы профилирования (анализ: QCacheGrind/KCacheGrind)
- `trace.*.xt.gz` — файлы трассировки (текстовые файлы, можно открыть в редакторе)

Оба типа файлов сжаты gzip, нужно распаковать перед использованием.

---

## Альтернативы

### Excimer (Sentry)

Быстрый профайлер от Sentry, альтернатива Xdebug для профилирования:

```bash
apt-get install php-excimer
```

Документация: https://docs.sentry.io/platforms/php/profiling/

---

## Полезные ссылки

- **Официальная документация:** https://xdebug.org/docs/all_settings
- **YouTube канал Derick Rethans (создатель Xdebug):** https://www.youtube.com/@DerickRethansXdebug/videos
- **DBGp Proxy:** https://www.youtube.com/watch?v=_3RkGZK-UC8
- **Flamegraph:** https://www.youtube.com/watch?v=4EocpeKxI0k
- **QCacheGrind:** https://www.youtube.com/watch?v=VJMLygu3I3M
