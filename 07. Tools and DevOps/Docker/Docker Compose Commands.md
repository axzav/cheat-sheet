# Docker Compose Commands


```shell
docker-compose --version
docker-compose up --build
docker-compose build --no-cache [service-name] # Сбилдить образ и пересоздать контейнер для сервиса

docker-compose --version - старая команда
docker compose version - новая команда, compose идет как плагин вместе с докер
```

## Запуск

```bash
docker compose up
docker compose up -d          # в фоне
docker compose up --build     # пересобрать
docker compose up api         # только один сервис
```

---

## Остановка

```bash
docker compose down
docker compose down -v        # удалить volume
docker compose stop
docker compose start
```

---

## Логи

```bash
docker compose logs
docker compose logs -f
docker compose logs api
```

---

## Exec

```bash
docker compose exec api bash
docker compose run api bash
```

Разница:

* `exec` — в уже запущенном контейнере
* `run` — временный контейнер

---

## Build

```bash
docker compose build
docker compose build --no-cache
```

---

## Масштабирование

```bash
docker compose up --scale api=3
```

(работает лучше без container_name)

---

## Статус

```bash
docker compose ps
docker compose top
```

---

## Cleanup

```bash
docker compose down --remove-orphans
docker compose rm
```

---

## Полезные флаги

```bash
-f docker-compose.prod.yml   # другой файл
-p project-name              # имя проекта
--env-file .env.prod
```

Пример:

```bash
docker compose -f docker-compose.prod.yml up -d
```

---

## Override файлы

Compose автоматически мерджит:

```
docker-compose.yml
docker-compose.override.yml
```

Полезно для dev.

---