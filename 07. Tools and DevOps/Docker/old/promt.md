У меня есть такая заметка по командам докера. Поправь ее, исправь неточности, убери дубликаты и добавь чего не хватает. Сохрани стиль оформления


# Docker commands

## Images
shell
docker images  # Для отображения списка локальных образов (-a intermediate, -q только id)
docker images -aq  # -a : список всех, включая временные, -q : вывести только id контейнеров|список образов в системе

docker build -t image-name . # создать образ с именем "image-name" на основе Dockerfile из текущей директории
docker build -t my_image:0.01 . # Для билда образа на основе Dockerfile

docker tag [image_id] my_image:0.01   # Также добавить тег к образу можно отдельной командой

docker pull nginx:1.15.1
docker pull image-name  # загрузить образ "image-name" из registry
docker push username/project-name # Отправить докер образ в registry

docker rmi [image_id] # Удаление образа по id или repository:tag
docker rmi -f $(docker images -aq) # Удалить все образы , -f : force remove

docker stats # статистика по используемым ресурсам

docker system prune -a # To clear all docker entities

## Containers
sh
docker ps -a # Вывести список существующих (-a) в системе контейнеров
docker ps -a # список запущенных и остановленных, -q : вывести только id контейнеров
docker ps    # Список запущенных контейнеров

docker inspect [container-name] # Получить подробную информацию о конфигурации контейнера
docker logs containerid # Логи контейнера

docker rm [container-name] # Удалить контейнер
docker rm -fv $(docker ps -aq) # Удалить все контейнеры (-f : force remove running container, -v : remove anonymous volumes)


docker cp core-db:/home/dump.sql .                   # copy?
docker cp ~/dump.sql c5605ccfb0a8:/home/dump.sql     # copy?

**docker run = docker create + docker start**

При `docker run` или `docker create` создается контейнер с иммутабельной конфигурацией. Это значит, что после создания контейнера его можно только запустить с уже назначенными volumes, ports, etc, но нельзя их изменить в docker start.

Если в `docker run` или `docker create` была передана опция `--name`, то при повторном запуске создания контейнера с тем же именем будет ошибка

При указании `[COMMAND]` в  `docker run`/`docker start` будет перезаписан `CMD` из `Dockerfile`
shell
docker create image-name # Создать контейнер на основе image-name
docker start container-name [COMMAND] # Запустить контейнер и передать в CMD [COMMAND] (опционально)

docker run image-name [COMMAND] # Создать контейнер на основе image-name, передать в CMD [COMMAND] (опционально) и запустить

docker stop container-name # stop container with name "container-name"
docker stop $(docker ps -q) # stop all active containers
docker kill [container-name]

docker pause/unpause [container-name]

shell
docker run --name nodetest node -p 80:8080 -v ./:/var/www/node -it bash
docker run --name container-name image-name |--name : name of container<br/>-d : detach from running container<br/>--rm : to remove container after stop<br/>-p 80:8080 : to bind 8080 container port to 80 host machine port<br/>-v /host/dir:/container/dir : to mount host dir with container dir|запустить контейнер с именем "container-name" на основе образа "image-name"


docker run -it - отменяет запуск CMD в dockerfile???

docker exec -it nodetest bash
docker exec b2c4cb98bccd /usr/bin/mysqldump -u root --password=repol pwp_db > init.sql
docker exec -it ubuntu_container /bin/sh - interactive console

# Что это??? exec sh -c
docker exec some-mysql sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /some/path/on/your/host/all-databases.sql


`create` и `run` имеют одинаковые параметры за исключением того, что `run` принимает еще и **[COMMAND]**
sh
docker run \                                                                     
--name nignx_test3 \
-p 8080:80 \
--rm \
-d \
-v /host/dir:/container/dir \
-e MYSQL_ROOT_PASSWORD=my-secret-pw \
nginx:1.23

* `--name` | Имя, с которым будет создан новый контейнер
* `--rm` | Удалить контейнер после завершения работы
* `-p 8080:80` | Привязать порт **8080** хоста к порту **80** контейнера
* `-d` | Detach mode. По умолчанию консоль будет подключена к выводу контейнера
* `-v /host/dir:/container/dir` | Монтирование директории хоста в директорию контейнера
* `-e MYSQL_ROOT_PASSWORD=pass` | Переменные окружения для контейнера |


## Volumes
shell
docker volume ls # list of volumes
docker volume create volume-name # create volume with name "volume-name"

docker volume inspect docker_db_data
docker volume rm db_data

shell
docker run -v /host/datadir:/var/lib/mysql:ro mysql
docker run -v /var/lib/mysql mysql # Anonymus volume создает докер автоматически (где-то в var/lib/docker/volumes/HASH/_data)
docker run -v mysql_data:/var/lib/mysql mysql # Named volume


Вот исправленная и дополненная версия. Я убрал дубликаты, поправил неточности (run/start, -it, CMD, порядок флагов, пояснения), добавил часто используемые команды (attach, restart, top, diff, network, prune частично) и сохранил стиль.

# Docker commands

## Images

```shell
docker images                 # Список локальных образов (`-a` все включая intermediate, `-q` только id)
docker images -aq             # id всех образов

docker build -t image-name .          # создать образ из Dockerfile текущей директории
docker build -t my_image:0.01 .       # билд с тегом

docker tag [image_id] my_image:0.01   # добавить тег существующему образу

docker pull nginx:1.15.1              # загрузить образ из registry
docker pull image-name
docker push username/project-name     # отправить образ в registry

docker rmi [image_id]                 # удалить образ по id или repository:tag
docker rmi -f $(docker images -aq)    # удалить все образы (force)

docker history image-name             # история слоев образа
docker stats                          # статистика использования ресурсов контейнерами

docker system prune -a                # очистить неиспользуемые контейнеры, сети, образы, кеш
docker image prune -a                 # очистить неиспользуемые образы
Containers
docker ps            # запущенные контейнеры
docker ps -a         # все контейнеры
docker ps -aq        # id всех контейнеров

docker inspect [container-name]   # подробная информация о контейнере
docker logs container-id          # логи (`-f` follow, `--tail`)

docker rm [container-name]        # удалить контейнер
docker rm -fv $(docker ps -aq)    # удалить все контейнеры (force + volumes)

docker cp core-db:/home/dump.sql .               # копировать файл из контейнера на хост
docker cp ~/dump.sql c5605ccfb0a8:/home/dump.sql # копировать файл в контейнер

docker run = docker create + docker start

Контейнер создается с иммутабельной конфигурацией (ports, volumes, env).
После создания изменить их нельзя — нужно пересоздать контейнер.

Если указать --name, повторное создание контейнера с тем же именем вызовет ошибку.

CMD / ENTRYPOINT

[COMMAND] в docker run перезаписывает CMD

ENTRYPOINT не перезаписывается (если не использовать --entrypoint)

docker start не принимает COMMAND — он просто запускает существующий контейнер

docker create image-name          # создать контейнер
docker start container-name       # запустить существующий

docker run image-name [COMMAND]   # создать + запустить контейнер

docker stop container-name
docker stop $(docker ps -q)       # остановить все запущенные

docker kill [container-name]      # принудительно SIGKILL
docker restart [container-name]

docker pause [container-name]
docker unpause [container-name]

docker attach [container-name]    # подключиться к STDIN/STDOUT
docker exec -it container bash    # запустить процесс внутри контейнера
docker top container-name         # процессы контейнера
docker diff container-name        # изменения файловой системы
run параметры
docker run \
--name nginx_test \
-p 8080:80 \
--rm \
-d \
-v /host/dir:/container/dir \
-e MYSQL_ROOT_PASSWORD=my-secret-pw \
nginx:1.23

--name | имя контейнера

--rm | удалить контейнер после остановки

-p 8080:80 | проброс порта host → container

-d | detach (фон)

-it | interactive tty (консоль)

-v host:container | mount директории

-e KEY=value | env переменные

Пример:

docker run -it ubuntu bash   # интерактивная консоль

👉 -it не отменяет CMD, а просто делает stdin/tty интерактивным.
CMD заменяется только если передать свой COMMAND.

exec примеры
docker exec -it nodetest bash

docker exec b2c4cb98bccd /usr/bin/mysqldump -u root --password=repol pwp_db > init.sql

docker exec some-mysql sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /host/all.sql

sh -c нужен, чтобы выполнить сложную shell-команду внутри контейнера.

Volumes
docker volume ls
docker volume create volume-name

docker volume inspect docker_db_data
docker volume rm db_data

docker volume prune      # удалить неиспользуемые volume

Типы:

docker run -v /host/datadir:/var/lib/mysql:ro mysql   # bind mount (read-only)
docker run -v /var/lib/mysql mysql                    # anonymous volume
docker run -v mysql_data:/var/lib/mysql mysql         # named volume

Anonymous volume создается автоматически и хранится в /var/lib/docker/volumes/...

Networks (часто забывают)
docker network ls
docker network create mynet
docker network inspect mynet
docker network rm mynet

docker run --network mynet nginx
Полезные cleanup команды
docker container prune
docker image prune
docker volume prune
docker network prune
docker system df        # сколько места занимает docker

---

Если хочешь, могу дальше:

✅ сделать версию “production шпаргалка” (самые нужные 20 команд)  
✅ объяснить docker run vs exec vs attach — это критически важно  
✅ разобрать volume vs bind mount (очень частая путаница)  
✅ объяснить ENTRYPOINT vs CMD — там много нюансов  
✅ дать real-world best practices (например почему не надо `latest`)
Сделай шпаргалку по docker compose примерно в том же формате что и шпаргалка выше. Укажи там параметры yaml, зачем они нужны, какие есть и что делают. Также укажи консольные команды
Writing
Docker Compose cheat sheet
Основная идея

Docker Compose — декларативное описание нескольких контейнеров (stack) в одном docker-compose.yml.

Позволяет:

поднимать несколько сервисов одной командой

описывать сети, volume, env

управлять зависимостями сервисов

переиспользовать конфигурацию

Структура docker-compose.yml
version: "3.9"

services:
  app:
    image: node:20
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    environment:
      NODE_ENV: production

volumes:
  db_data:

networks:
  backend:
services

Главный раздел — список контейнеров.

services:
  api:
    image: my-api:latest

Каждый сервис = контейнер.

Основные параметры сервиса
image
image: nginx:1.25

Использовать готовый образ.

build
build: .
build:
  context: .
  dockerfile: Dockerfile.dev

Собрать образ локально.

context — директория билда

dockerfile — альтернативный Dockerfile

container_name
container_name: my-api

Явное имя контейнера (обычно не рекомендуется — ломает scaling).

ports
ports:
  - "8080:80"

host → container.

volumes
volumes:
  - .:/app
  - db_data:/var/lib/mysql

bind mount

named volume

environment
environment:
  NODE_ENV: production
  DB_HOST: db

Можно:

environment:
  - NODE_ENV=production
env_file
env_file:
  - .env

Загрузить env из файла.

command
command: npm run start

Перезаписать CMD.

entrypoint
entrypoint: ["sh", "-c"]

Перезаписать ENTRYPOINT.

depends_on
depends_on:
  - db

Гарантирует порядок запуска (не readiness).

restart
restart: always

Политика:

no

always

on-failure

unless-stopped

networks
networks:
  - backend

Подключить сервис к сети.

healthcheck
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s
  timeout: 5s
  retries: 3

Проверка живости контейнера.

working_dir
working_dir: /app

Рабочая директория.

user
user: node

Запуск от другого пользователя.

extra_hosts
extra_hosts:
  - "host.docker.internal:host-gateway"

Дополнительные записи в hosts.

deploy (чаще для swarm)
deploy:
  replicas: 3
  resources:
    limits:
      cpus: "0.5"
      memory: 512M

Scaling / лимиты.

volumes (верхний уровень)
volumes:
  db_data:

Можно:

volumes:
  db_data:
    driver: local
networks (верхний уровень)
networks:
  backend:
    driver: bridge

Compose автоматически создаёт сеть по умолчанию.

Пример real stack
services:
  api:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
CLI команды
Запуск
docker compose up
docker compose up -d          # в фоне
docker compose up --build     # пересобрать
docker compose up api         # только один сервис
Остановка
docker compose down
docker compose down -v        # удалить volume
docker compose stop
docker compose start
Логи
docker compose logs
docker compose logs -f
docker compose logs api
Exec
docker compose exec api bash
docker compose run api bash

Разница:

exec — в уже запущенном контейнере

run — временный контейнер

Build
docker compose build
docker compose build --no-cache
Масштабирование
docker compose up --scale api=3

(работает лучше без container_name)

Статус
docker compose ps
docker compose top
Cleanup
docker compose down --remove-orphans
docker compose rm
Полезные флаги
-f docker-compose.prod.yml   # другой файл
-p project-name              # имя проекта
--env-file .env.prod

Пример:

docker compose -f docker-compose.prod.yml up -d
.env поведение

Compose автоматически читает .env рядом с yaml.

Использование:

image: myapp:${TAG}
Override файлы

Compose автоматически мерджит:

docker-compose.yml
docker-compose.override.yml

Полезно для dev.

Частые best practices

не использовать container_name

всегда фиксировать версии образов

volume для данных БД

depends_on + healthcheck

отдельные файлы dev/prod

.env для секретов

Если хочешь — дальше могу:

✅ объяснить архитектуру compose сети (очень важно)
✅ объяснить env interpolation (там есть подводные камни)
✅ показать production compose шаблон
✅ объяснить dev workflow (hot reload, bind mounts)
✅ разобрать частые anti-patterns
✅ объяснить difference compose vs k8s — очень полезно

Я собираю docker-compose пример для своих заметок, сейчас есть такой


yaml

services: # Главный раздел — список контейнеров, Каждый сервис = контейнер
    webserver:
        image: nginx:1.23                    # Использовать готовый образ
        container_name: app_webserver        # Явное имя контейнера (обычно не рекомендуется — ломает scaling)
        ports:
            - "8081:80"                      # host → container
        depends_on:
            - core-api-app                   # Гарантирует порядок запуска (не readiness)
            - one-login-app
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
            - 3306
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


Добавь в него сервисы в которых можно описать примеры следующих опций - command, entrypoint, working_dir

Добавь их так, чтобы это было похоже на реальное использование со смыслом

Вот расширенная версия твоего compose-примера.
Я добавил два реалистичных сервиса:

queue-worker — пример с command и working_dir (типичный worker для Laravel / Symfony / PHP).

db-migrations — пример с entrypoint (контейнер, который выполняет миграции при запуске).

Все добавлено логично, без абстрактных "hello world".

services: # Главный раздел — список контейнеров, Каждый сервис = контейнер

    webserver:
        image: nginx:1.23
        container_name: app_webserver
        ports:
            - "8081:80"
        depends_on:
            - core-api-app
            - one-login-app
        networks:
            default:
                aliases:
                    - api-core.app.local
                    - api-one-login.app.local
        volumes:
            - ./../api-core:/var/www/api-core
            - ./../one-login:/var/www/one-login
            - ./webserver/api.one-login.local.conf:/etc/nginx/conf.d/api.one-login.local.conf:ro
            - ./webserver/api.core.local.conf:/etc/nginx/conf.d/api.core.local.conf:ro

    core-api-app:
        build:
            context: .
            dockerfile: core-api-app/Dockerfile
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

    # 👇 Пример использования working_dir + command
    queue-worker:
        build:
            context: .
            dockerfile: core-api-app/Dockerfile
        container_name: app_queue-worker
        depends_on:
            - core-api-db
            - redis
        volumes:
            - ./../api_backoffice:/var/www
            - ./shared:/var/www/_shared
        working_dir: /var/www          # рабочая директория внутри контейнера
        command: php artisan queue:work --sleep=3 --tries=3
        restart: unless-stopped
        # command перезаписывает CMD из Dockerfile
        # типичный отдельный сервис-воркер

    # 👇 Пример entrypoint для одноразовой задачи (миграции)
    db-migrations:
        build:
            context: .
            dockerfile: core-api-app/Dockerfile
        container_name: app_db-migrations
        depends_on:
            - core-api-db
        volumes:
            - ./../api_backoffice:/var/www
        working_dir: /var/www
        entrypoint: ["php", "artisan"]   # перезаписываем ENTRYPOINT
        command: ["migrate", "--force"]
        # entrypoint + command = php artisan migrate --force
        # используется для CI/CD или docker compose run db-migrations

    core-api-db:
        image: mysql:8.0.44
        container_name: app_core-api-db
        expose:
            - 3306
        env_file:
            - ./core-api-app/.env_db.docker
        ports:
            - 3316:3306
        volumes:
            - core-api-db_data:/var/lib/mysql
            - ./core-api-db/my.cnf:/etc/mysql/conf.d/my.cnf:ro

    # 👇 Пример redis для полноты depends_on
    redis:
        image: redis:7
        container_name: app_redis
        ports:
            - "6379:6379"

volumes:
    core-api-db_data:
    one-login-db_data:
Что здесь важно понимать
🔹 command

Перезаписывает CMD из Dockerfile.

Используется когда:

один и тот же образ нужен для разных задач

нужно изменить режим запуска (worker, scheduler, cli)

🔹 entrypoint

Перезаписывает ENTRYPOINT.

Используется когда:

нужно полностью изменить точку входа

контейнер становится "одноразовым CLI инструментом"

🔹 working_dir

Меняет рабочую директорию внутри контейнера.

Важно для:

artisan / composer / npm

корректных relative path

entrypoint-скриптов

Реальный workflow
# поднять все сервисы
docker compose up -d

# запустить миграции отдельно
docker compose run --rm db-migrations

# зайти в приложение
docker compose exec core-api-app bash

# посмотреть логи воркера
docker compose logs -f queue-worker

Если хочешь, дальше могу:

✅ добавить пример scheduler (cron внутри контейнера)
✅ показать dev vs prod вариант этого compose
✅ показать healthcheck + depends_on condition
✅ показать anti-pattern с container_name и почему scaling ломается
✅ объяснить когда лучше command, а когда entrypoint

Это уже будет уровень "я реально понимаю compose", а не просто использую 🙂

Расскажи мне подробно обо все что связано с environment docker compose
Я могу указать environment, env_file. Будут ли они конфликтовать или перезаписывать друг друга? В каком порядке?
Также еще есть .env файл. Переменные из него будут прокидываться в контейнеры?


Отличный вопрос.
В docker compose есть три разных уровня environment, и их часто путают:

.env (файл рядом с compose)

env_file (внутри сервиса)

environment (внутри сервиса)

Это три разные механики.

Разберём всё по порядку и очень чётко 👇

1️⃣ .env — файл для подстановки в compose

Файл .env, который лежит рядом с docker-compose.yml, используется для интерполяции переменных в YAML, а не напрямую для контейнера.

Пример:

APP_PORT=8081
TAG=1.23
services:
  web:
    image: nginx:${TAG}
    ports:
      - "${APP_PORT}:80"

Compose:

Загружает .env

Подставляет значения в YAML

И только потом создаёт контейнер

⚠️ Важно

Переменные из .env НЕ прокидываются автоматически в контейнер, если ты их явно не указал в environment или env_file.

2️⃣ environment — явные переменные контейнера
services:
  app:
    environment:
      APP_ENV: production
      DEBUG: "false"

Это прямой способ задать переменные окружения контейнеру.

Можно писать и так:

environment:
  - APP_ENV=production
  - DEBUG=false
3️⃣ env_file — загрузка переменных в контейнер
services:
  app:
    env_file:
      - .env.app

Файл .env.app:

APP_ENV=production
DEBUG=false

Эти переменные будут переданы в контейнер.

🔥 ПОРЯДОК ПРИОРИТЕТОВ (кто кого перезаписывает)

Если переменная объявлена в нескольких местах:

services:
  app:
    env_file:
      - .env.app
    environment:
      APP_ENV: local

И в .env.app есть:

APP_ENV=production

👉 environment выигрывает.

Итоговый приоритет (сильнее сверху):

environment в compose

env_file

Dockerfile ENV

переменные базового образа

💡 Взаимодействие с .env

Предположим:

.env:

APP_ENV=production

Compose:

services:
  app:
    environment:
      APP_ENV: ${APP_ENV}

Тогда:

Compose возьмёт значение из .env

Подставит его

И передаст в контейнер

👉 Но если ты не укажешь ${APP_ENV}, то переменная НЕ попадёт в контейнер.

4️⃣ Переменные shell

Compose также видит переменные из текущей shell-сессии:

export APP_ENV=staging
docker compose up

Если в YAML есть:

environment:
  APP_ENV: ${APP_ENV}

То shell-переменная перекроет .env.

🔥 Полный порядок интерполяции (для ${VAR})

Когда compose видит ${VAR}, он ищет:

Переменные текущей shell

.env файл

default значение ${VAR:-default}

Пример:

environment:
  APP_ENV: ${APP_ENV:-local}
🧠 Важно понимать разницу
Механизм	Используется для	Передаётся в контейнер
.env	интерполяция YAML	❌ нет
env_file	env контейнера	✅ да
environment	env контейнера	✅ да
⚠️ Частая ошибка

Люди думают:

Я положил переменную в .env, значит контейнер её увидит

❌ Нет.

Она попадёт в контейнер только если:

environment:
  MY_VAR: ${MY_VAR}

или через env_file.

🔎 Что если одинаковые переменные?

Пример:

env_file:
  - .env.app

environment:
  DB_HOST: db

.env.app:

DB_HOST=localhost

👉 В контейнере будет:

DB_HOST=db

environment всегда перекрывает env_file.

🏗 Реальный production pattern

Обычно делают так:

.env (для compose)
APP_PORT=8081
TAG=1.2.3
.env.app (для контейнера)
APP_ENV=production
DB_HOST=db
compose
services:
  app:
    image: myapp:${TAG}
    ports:
      - "${APP_PORT}:80"
    env_file:
      - .env.app
💣 Важный нюанс

.env используется только для одного файла compose.

Если ты запускаешь:

docker compose -f docker-compose.prod.yml up

Compose всё равно ищет .env в текущей директории.

🧪 Как посмотреть итоговый конфиг

Очень полезная команда:

docker compose config

Она покажет:

все переменные после интерполяции

merged файлы

итоговый YAML

🎯 Резюме
Есть два разных уровня:
🔹 Уровень compose (подстановка)

.env

shell переменные

${VAR}

🔹 Уровень контейнера

environment

env_file

Dockerfile ENV

Если хочешь глубже

Могу разобрать:

interpolation edge cases ($$, ${VAR?err})

как работают secrets в compose

почему не стоит хранить пароли в env

как правильно делать dev/prod env стратегию

что меняется в compose v2

Скажи уровень — сделаем от "понять" до "архитектурно правильно" 😄

Если в Dockerfile была определена переменная окружения, то на этапе билда будет все равно использоваться она? Т.е. если в docker compose переопределяется эта переменная то на этапе билда будет использоваться переменная из Dockerfile?

Но как тогда работают образы баз данных которые берут данные из env для создания пользователей и баз?

Очень хороший вопрос — тут как раз проходит граница между:

🔨 этапом сборки образа (build)

🚀 этапом запуска контейнера (runtime)

И Dockerfile + Compose работают на разных этапах.

Разберём по порядку.

1️⃣ Dockerfile: ENV — это runtime, не build (почти всегда)

Если в Dockerfile написано:

ENV APP_ENV=production

Это:

задаёт переменную окружения контейнера

работает при запуске контейнера

не является build-time переменной

👉 То есть это runtime значение по умолчанию.

2️⃣ Что происходит если в compose есть environment
services:
  app:
    environment:
      APP_ENV: local

👉 В этом случае:

во время build используется то, что определено в Dockerfile

при запуске контейнера значение будет local

environment из compose перекрывает ENV из Dockerfile

🔥 Приоритет runtime переменных

При запуске контейнера:

environment (compose)

env_file

ENV из Dockerfile

базовый образ

3️⃣ А что тогда используется на этапе build?

На этапе build используются только ARG, а не ENV.

Пример:

ARG APP_ENV=production
RUN echo $APP_ENV

И в compose:

services:
  app:
    build:
      context: .
      args:
        APP_ENV: local

👉 Тогда во время сборки будет local.

💡 Главное различие
Тип	Работает когда	Можно переопределить в compose
ENV	runtime	✅ через environment
ARG	build time	✅ через build.args
.env	interpolation	❌ не влияет напрямую
4️⃣ Ответ на твой главный вопрос

Если в Dockerfile была определена переменная окружения, то на этапе билда будет использоваться она?

Если это ENV — она НЕ влияет на build как build-параметр.
Она влияет только на runtime.

Если это ARG — тогда да, используется при build.

5️⃣ Тогда как работают образы баз данных?

Отличный вопрос 👇

Возьмём официальный образ MySQL.

В Dockerfile там есть примерно так:

ENV MYSQL_ROOT_PASSWORD=""

Но настоящая магия — в ENTRYPOINT скрипте.

У MySQL образа есть:

ENTRYPOINT ["docker-entrypoint.sh"]

А внутри docker-entrypoint.sh:

if [ -n "$MYSQL_ROOT_PASSWORD" ]; then
   # создать root пользователя
fi

👉 То есть:

Контейнер стартует

ENTRYPOINT-скрипт читает env

Создаёт базу / пользователей

Потом запускает mysqld

🔥 Ключевой момент

База НЕ создаётся на этапе build.

Она создаётся на этапе runtime при первом запуске контейнера.

6️⃣ Почему это работает с compose
services:
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: secret

Процесс:

Контейнер стартует

ENV доступен entrypoint-скрипту

Скрипт создаёт root-пользователя

Данные сохраняются в volume

При повторном запуске инициализация не повторяется

7️⃣ Очень важный нюанс

Инициализация MySQL/Postgres происходит ТОЛЬКО если:

/var/lib/mysql пустой

То есть:

если volume уже содержит данные

переменные окружения больше не влияют

Это частая ловушка.

8️⃣ Сценарий чтобы стало кристально ясно

Dockerfile:

ENV APP_ENV=production

Compose:

services:
  app:
    build:
      context: .
    environment:
      APP_ENV: local
Что будет?
Этап	Значение
build	production (если используется ENV внутри RUN)
runtime	local
9️⃣ Если ENV используется в RUN?

Если в Dockerfile:

ENV APP_ENV=production
RUN echo $APP_ENV

То на этапе build будет production.

Но если ты хочешь контролировать это извне — нужно использовать ARG:

ARG APP_ENV=production
ENV APP_ENV=$APP_ENV

Тогда можно управлять и build, и runtime.

🎯 Итог
ENV в Dockerfile:

runtime default

переопределяется compose

ARG:

build-time

управляется через build.args

Базы данных:

читают env при runtime

используют entrypoint-скрипт

инициализация происходит только при пустом volume

Если хочешь — могу дальше разобрать:

правильный паттерн ARG + ENV вместе

как сделать build-time и runtime конфиг чисто

почему секреты нельзя хранить в ENV

как работают docker secrets

что происходит если менять env и не пересоздавать контейнер

Тут уже начинается уровень “понимаю Docker архитектурно” 😄