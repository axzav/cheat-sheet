# Docker Compose

**Docker Compose** — декларативное описание нескольких контейнеров (stack) в одном `docker-compose.yml`.

Позволяет:

* поднимать несколько сервисов одной командой
* описывать сети, volume, env
* управлять зависимостями сервисов
* переиспользовать конфигурацию


**Best practices:**
* Не использовать `container_name` (ломает масштабирование)
* Всегда фиксировать версии образов (не использовать `latest`)
* Использовать volumes для данных БД
* Использовать `depends_on` + `healthcheck` для управления зависимостями
* Отдельные файлы для dev/prod окружений
* Использовать `.env` для секретов и конфигурации
* Использовать `COMPOSE_PROJECT_NAME` для избежания конфликтов имен


![](./_img/docker_compose.png)

---

```yaml

services:  # Главный раздел — список контейнеров. Каждый сервис = контейнер
    webserver:
        image: nginx:1.23                    # Использовать готовый образ
        container_name: app_webserver        # Явное имя контейнера (обычно не рекомендуется — ломает scaling)
        ports:
            - "8081:80"                      # host → container
        depends_on:
            - core-api-app                   # Гарантирует порядок запуска (не readiness!)
            - one-login-app                  # Для readiness использовать healthcheck
        networks:
            default:
                aliases: #webserver is a main requests router so we need to specify hosts for communication
                    - api-core.app.local
                    - api-one-login.app.local
        volumes:
            - ./../api-core:/var/www/api-core    # bind mount
            - ./../one-login:/var/www/one-login
            - ./webserver/api.one-login.local.conf:/etc/nginx/conf.d/api.one-login.local.conf:ro
            - ./webserver/api.core.local.conf:/etc/nginx/conf.d/api.core.local.conf:ro

    core-api-app:
        build:     # Собрать образ локально
            context: .  # директория билда
            dockerfile: core-api-app/Dockerfile  # альтернативный Dockerfile
        container_name: app_core-api-app
        depends_on:
            - core-api-db
            - redis
        volumes:
            - ./../api_backoffice:/var/www
            - ./shared:/var/www/_shared
            - ./core-api-app/config/php.ini:/usr/local/etc/php/conf.d/custom.ini
            - ./core-api-app/xdebug:/tmp/xdebug
        environment:
            XDEBUG_MODE: 'debug'
            XDEBUG_CONFIG: 'client_host=host.docker.internal client_port=9003'
            PHP_IDE_CONFIG: 'serverName=api-core-docker'

    core-api-db:
        image: mysql:8.0.44
        container_name: app_core-api-db
        expose:
            - 3306                           # Открыть порт только внутри Docker сети (не на хост)
        env_file:
            - ./core-api-app/.env_db.docker
        ports:
            - 3316:3306
        volumes:
            - core-api-db_data:/var/lib/mysql            # named volume
            - ./core-api-db/my.cnf:/etc/mysql/conf.d/my.cnf:ro

volumes:
    core-api-db_data:
    one-login-db_data:
```

---

## command / entrypoint

Перезаписать CMD / ENTRYPOINT из Dockerfile:

```yaml
entrypoint: ["php", "artisan"]   # Перезаписывает ENTRYPOINT из Dockerfile
command: ["migrate", "--force"]  # Перезаписывает CMD из Dockerfile
```

Если указан только `command`, он будет использован как аргументы для `ENTRYPOINT`.


## Environments

Есть 2 способа передать переменные окружения в сервис:

**1. `env_file`** — загрузить переменные из файла:
```yaml
env_file:
    - .env.app   # Загружает переменные окружения из указанных файлов. Каждая строка в формате `KEY=value`.
    - .env.prod
```

**2. `environment`** — указать переменные напрямую:
```yaml
environment:
    APP_ENV: local
    DB_HOST: db
    NODE_ENV: production
```

Или в формате массива:
```yaml
environment:
    - NODE_ENV=production
    - DB_HOST=db
```

**Приоритет переменных окружения:**
1. `environment` (высший приоритет)
2. `env_file`
3. Переменные из Dockerfile (`ENV`)
4. Переменные из базового образа

Переменные окружения из docker-compose перезаписывают переменные из базового образа или Dockerfile.


## .env файл

Compose автоматически читает `.env` файл в той же директории, что и `docker-compose.yml`.

**Использование в docker-compose.yml:**

```yaml
image: myapp:${TAG}
ports:
  - "${NGINX_PORT}:80"
environment:
  DB_HOST: ${DB_HOST:-localhost}  # Значение по умолчанию
```

**Пример .env:**
```env
TAG=1.0.0
NGINX_PORT=8080
DB_HOST=db
MYSQL_ROOT_PASSWORD=secret
```

## networks

Docker Compose по умолчанию создает общую сеть для всех сервисов, в которой каждый сервис доступен по имени сервиса. В большинстве случаев дополнительная настройка не требуется.

**Именованные сети:**

```yaml
networks:
  backend:
    driver: bridge
  frontend:
    driver: bridge
```

**Использование в сервисе:**

```yaml
services:
  api:
    networks:
      - backend
  web:
    networks:
      - frontend
      - backend
```

**Изменение default сети:**

Если нужно настроить стандартную сеть, можно обращаться к ней как к `default`:

```yaml
services:
  webserver:
    networks:
      default:
        aliases:
          - api.core.local
          - api.one-login.local
```

**Типы драйверов сетей:**
- `bridge` — по умолчанию, изолированная сеть
- `host` — использует сеть хоста (только Linux)
- `none` — отключает сеть


## depends_on

Управляет порядком запуска и остановки контейнеров:

```yaml
services:
  web:
    depends_on:
      - db
      - redis
  db:
    image: postgres
```

**Важно:** `depends_on` гарантирует только порядок запуска, но не готовность сервиса. Для ожидания готовности нужно использовать `healthcheck`:

```yaml
services:
  web:
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
```

**Условия depends_on:**
- `service_started` — сервис запущен (по умолчанию)
- `service_healthy` — сервис прошел healthcheck
- `service_completed_successfully` — сервис завершился успешно (для одноразовых задач)

---

## restart

Политика перезапуска контейнера:

```yaml
restart: always
```

**Политики перезапуска:**

* `no` — не перезапускать (по умолчанию)
* `always` — всегда перезапускать при остановке
* `on-failure` — перезапускать только при ошибке (ненулевой код выхода)
* `unless-stopped` — перезапускать всегда, кроме случаев ручной остановки

---

## healthcheck

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s          # Интервал между проверками
  timeout: 10s            # Таймаут проверки
  retries: 3             # Количество попыток перед пометкой как unhealthy
  start_period: 40s      # Период запуска (неудачные проверки не считаются)
```

**Примеры проверок:**

```yaml
# HTTP endpoint
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost/health"]

# База данных
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U postgres"]
  
# Простая команда
healthcheck:
  test: ["CMD", "redis-cli", "ping"]
```

**Использование с depends_on:**

```yaml
services:
  app:
    depends_on:
      db:
        condition: service_healthy
```

## working_dir

Рабочая директория для команд:

```yaml
working_dir: /app
```

## user

Пользователь, от имени которого запускается процесс:

```yaml
user: node
user: "1000:1000"  # UID:GID
```

## ports

Проброс портов:

```yaml
ports:
  - "8080:80"              # host:container
  - "127.0.0.1:8080:80"    # С указанием IP хоста
  - "8080-8090:80-90"      # Диапазон портов
```

**expose vs ports:**
- `ports` — пробрасывает порт на хост
- `expose` — открывает порт только внутри Docker сети

## volumes

Монтирование томов и директорий:

```yaml
volumes:
  - /host/dir:/container/dir        # Bind mount
  - ./local/dir:/container/dir     # Относительный путь
  - volume_name:/container/dir     # Named volume
  - /container/dir                 # Anonymous volume
  - ./config:/etc/app:ro            # Read-only mount
```

**Типы volumes:**
- **Bind mount** — монтирование директории хоста
- **Named volume** — управляемый Docker том
- **Anonymous volume** — автоматически создаваемый том

## build

Сборка образа из Dockerfile:

```yaml
build:
  context: .                        # Контекст сборки
  dockerfile: Dockerfile            # Путь к Dockerfile
  args:                             # Build arguments
    BUILDKIT_INLINE_CACHE: 1
  target: production                # Целевой stage (для multi-stage)
```

Или короткая форма:

```yaml
build: .
build: ./path/to/dockerfile
```
