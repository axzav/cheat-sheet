## 🧱 **2. Именование и порты**

### Контейнеры и сервисы:

* Для избежания конфликтов в именах контейнеров используй префиксы, уникальные для проекта, или настрой `COMPOSE_PROJECT_NAME`.
  Пример:

  ```bash
  export COMPOSE_PROJECT_NAME=phpplayground
  docker-compose up -d
  ```

  Это создаст контейнеры вида `phpplayground_php74_1`, `phpplayground_mysql57_1` и т.д.

### Порты:

* Жёсткое хардкодирование портов типа `8888`, `33061` и `6379` удобно, но может конфликтовать, если ты запускаешь несколько подобных сетапов.
  Лучше использовать `.env`:

  ```env
  NGINX_PORT=8888
  MYSQL_PORT=33061
  REDIS_PORT=6379
  ```

  И в `docker-compose.yaml`:

  ```yaml
  ports:
    - "${NGINX_PORT}:80"
  ```