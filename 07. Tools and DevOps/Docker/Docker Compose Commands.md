# Docker Compose Commands

В новых версиях Docker (v20.10+) `docker-compose` заменен на `docker compose` (плагин). Старая команда `docker-compose` все еще работает, но рекомендуется использовать новый синтаксис.

```shell
# Старый синтаксис (docker-compose как отдельная утилита)
docker-compose --version

# Новый синтаксис (docker compose как плагин)
docker compose version
```

## Запуск

```bash
docker compose up                    # Запустить сервисы
docker compose up -d                 # Запустить в фоне (detached)
docker compose up --build            # Пересобрать образы перед запуском
docker compose up --no-build         # Не пересобирать образы (использовать существующие)
docker compose up --no-recreate      # Не пересоздавать контейнеры (использовать существующие)
docker compose up api                # Запустить только один сервис
docker compose up --scale api=3      # Запустить несколько экземпляров сервиса
```

## Остановка

```bash
docker compose down                  # Остановить и удалить контейнеры
docker compose down -v               # Остановить, удалить контейнеры и volumes
docker compose down --remove-orphans # Удалить контейнеры, не определенные в compose файле
docker compose stop                  # Остановить контейнеры (без удаления)
docker compose start                 # Запустить остановленные контейнеры
docker compose restart               # Перезапустить контейнеры
docker compose pause                 # Приостановить контейнеры
docker compose unpause               # Возобновить контейнеры
```

## Логи

```bash
docker compose logs                  # Логи всех сервисов
docker compose logs -f               # Следить за логами (follow)
docker compose logs --tail=100       # Последние 100 строк
docker compose logs api              # Логи конкретного сервиса
docker compose logs -f api           # Следить за логами конкретного сервиса
```


## Exec и Run

```bash
docker compose exec api bash             # Выполнить команду в запущенном контейнере
docker compose exec api sh -c "command"  # Выполнить команду без интерактивной оболочки
docker compose run api bash              # Создать временный контейнер и выполнить команду
docker compose run --rm api bash         # Создать временный контейнер и удалить после выполнения
```

**Разница:**
* `exec` — выполнить команду в уже запущенном контейнере
* `run` — создать новый временный контейнер (не зависит от `depends_on`, может использовать другой `command`)


## Build

```bash
docker compose build                 # Собрать образы для всех сервисов
docker compose build api             # Собрать образ для конкретного сервиса
docker compose build --no-cache      # Собрать без использования кеша
docker compose build --pull          # Обновить базовые образы перед сборкой
```

## Масштабирование

```bash
docker compose up --scale api=3      # Запустить 3 экземпляра сервиса api
```

Масштабирование работает только если не указан `container_name` в docker-compose.yml, т.к. каждый контейнер должен иметь уникальное имя.

## Статус

```bash
docker compose ps                    # Список контейнеров проекта
docker compose top                   # Процессы запущенных контейнеров
docker compose config                # Проверить и показать конфигурацию
docker compose config --services     # Список сервисов
docker compose config --volumes      # Список volumes
```

## Cleanup

```bash
docker compose down --remove-orphans # Удалить контейнеры, включая не определенные в compose
docker compose rm                    # Удалить остановленные контейнеры
docker compose rm -f                 # Удалить контейнеры (force, даже запущенные)
docker compose down --volumes        # Удалить контейнеры и volumes
```

## Полезные флаги

```bash
-f docker-compose.prod.yml                            # Указать другой compose файл
-f docker-compose.yml -f docker-compose.override.yml  # Объединить несколько файлов
-p project-name                                       # Имя проекта (префикс для контейнеров)
--env-file .env.prod                                  # Указать файл с переменными окружения
--profile production                                  # Запустить только сервисы с указанным профилем
```

**Примеры:**

```bash
# Использовать другой файл
docker compose -f docker-compose.prod.yml up -d

# Объединить несколько файлов (override имеет приоритет)
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d

# Указать имя проекта
docker compose -p myproject up -d

# Использовать переменные из другого файла
docker compose --env-file .env.prod up -d
```


## Override файлы

Compose автоматически мерджит следующие файлы (в порядке приоритета):

```
docker-compose.yml
docker-compose.override.yml
```

Файл `docker-compose.override.yml` автоматически используется для переопределения настроек в dev окружении. В продакшене можно использовать явное указание файлов:

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```
