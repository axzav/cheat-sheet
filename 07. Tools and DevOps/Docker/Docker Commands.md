# Docker commands

## Images

```shell
docker images                 # Список локальных образов
docker images -a              # Все образы (включая intermediate)
docker images -aq             # ID всех образов (`-a` все включая intermediate, `-q` только id)

docker build -t image-name .                        # создать образ из Dockerfile текущей директории
docker build -t my_image:0.01 .                     # билд с тегом
docker build -t image-name -f MyDockerfile .        # билд из докерфайла с кастомным именем, "." обозначает директорию контекста билда

docker tag [image_id] my_image:0.01   # добавить тег существующему образу

docker pull nginx:1.15.1              # загрузить образ из registry
docker pull image-name
docker push username/project-name     # отправить образ в registry

docker rmi [image_id]                 # удалить образ по id или repository:tag
docker rmi -f $(docker images -aq)    # удалить все образы (force)

docker history image-name             # История слоев образа
docker inspect image-name             # Подробная информация об образе
docker stats                          # Статистика использования ресурсов контейнерами (в реальном времени)

docker system prune                   # Очистить неиспользуемые контейнеры, сети, образы, кеш
docker system prune -a                # Очистить все неиспользуемые контейнеры, сети, образы (включая используемые образами), кеш
docker image prune                    # Очистить неиспользуемые образы
docker image prune -a                 # Очистить все неиспользуемые образы (включая используемые контейнерами)
```

---

## Containers

```sh
docker ps            # запущенные контейнеры
docker ps -a         # все контейнеры
docker ps -aq        # id всех контейнеров

docker inspect [container-name]   # подробная информация о контейнере
docker logs container-id          # логи (`-f` follow, `--tail`)

docker rm [container-name]        # Удалить остановленный контейнер
docker rm -f [container-name]     # Удалить контейнер (force, даже если запущен)
docker rm -fv $(docker ps -aq)    # Удалить все контейнеры (force + volumes)

docker cp core-db:/home/dump.sql .               # копировать файл из контейнера на хост
docker cp ~/dump.sql c5605ccfb0a8:/home/dump.sql # копировать файл в контейнер
```

**docker run = docker create + docker start**

Контейнер создается с иммутабельной конфигурацией (ports, volumes, env).
После создания изменить их нельзя — нужно пересоздать контейнер.

Если указать `--name`, повторное создание контейнера с тем же именем вызовет ошибку.

* `[COMMAND]` в `docker run` **перезаписывает CMD**
* ENTRYPOINT не перезаписывается (если не использовать `--entrypoint`)
* `docker start` **не принимает COMMAND** — он просто запускает существующий контейнер

```shell
docker create image-name          # создать контейнер
docker start container-name       # запустить существующий

docker run image-name [COMMAND]   # создать + запустить контейнер

docker stop container-name
docker stop $(docker ps -q)       # остановить все запущенные

docker kill [container-name]      # принудительно SIGKILL
docker restart [container-name]

docker pause [container-name]
docker unpause [container-name]

docker attach [container-name]    # Подключиться к STDIN/STDOUT (выход: Ctrl+P, Ctrl+Q)
docker exec -it container bash    # Запустить интерактивный процесс внутри контейнера
docker exec container command      # Выполнить команду в запущенном контейнере
docker top container-name         # Процессы контейнера
docker diff container-name        # Изменения файловой системы (A=added, D=deleted, C=changed)
```

```shell
docker run \
--name nginx_test \
-p 8080:80 \
--rm \
-d \
-v /host/dir:/container/dir \
-e MYSQL_ROOT_PASSWORD=my-secret-pw \
nginx:1.23
```

* `--name`: имя контейнера
* `--rm`: удалить контейнер после остановки
* `-p 8080:80`: проброс порта host → container
* `-d`: detach (фон)
* `-it`: interactive tty (консоль)
* `-v host:container[:options]`: mount директории (опции: `ro` - read-only, `rw` - read-write)
* `-e KEY=value`: env переменные
* `-u 1000:1000`: `<uid>[:<gid>]` запустить основной процесс контейнера с этим UID и GID

```shell
docker run -it ubuntu bash   # интерактивная консоль
```

`-it` **не отменяет CMD**, а просто делает stdin/tty интерактивным. CMD заменяется только если передать свой COMMAND.


```shell
docker exec -it nodetest bash

docker exec b2c4cb98bccd /usr/bin/mysqldump -u root --password=repol pwp_db > init.sql

docker exec some-mysql sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /host/all.sql
```

---

## Volumes

```shell
docker volume ls
docker volume create volume-name

docker volume inspect docker_db_data
docker volume rm db_data

docker volume prune      # удалить неиспользуемые volume
```

```shell
docker run -v /host/datadir:/var/lib/mysql:ro mysql   # Bind mount (read-only)
docker run -v /var/lib/mysql mysql                    # Anonymous volume
docker run -v mysql_data:/var/lib/mysql mysql         # Named volume
```

**Типы volumes:**
- **Bind mount**: монтирование директории хоста в контейнер (`/host:/container`)
- **Anonymous volume**: автоматически создается Docker, хранится в `/var/lib/docker/volumes/HASH/_data`
- **Named volume**: именованный том, управляемый Docker, хранится в `/var/lib/docker/volumes/volume-name/_data`

---

## Networks

```shell
docker network ls                    # Список сетей
docker network create mynet         # Создать сеть
docker network create --driver bridge mynet  # Создать сеть с указанием драйвера
docker network inspect mynet        # Подробная информация о сети
docker network rm mynet             # Удалить сеть
docker network prune                # Удалить неиспользуемые сети

docker run --network mynet nginx    # Запустить контейнер в сети
docker network connect mynet container  # Подключить контейнер к сети
docker network disconnect mynet container  # Отключить контейнер от сети
```

---

## Other

```shell
docker container prune              # Удалить остановленные контейнеры
docker image prune                  # Удалить неиспользуемые образы
docker volume prune                 # Удалить неиспользуемые volumes
docker network prune                # Удалить неиспользуемые сети
docker system df                    # Сколько места занимает Docker
docker system events                # События Docker в реальном времени
docker version                      # Версия Docker
docker info                         # Системная информация Docker
```
