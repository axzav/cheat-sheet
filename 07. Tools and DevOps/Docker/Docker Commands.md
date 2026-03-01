# Docker commands

## Images

```shell
docker images                 # Список локальных образов 
docker images -aq             # id всех образов (`-a` все включая intermediate, `-q` только id)

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
```

---

## Containers

```sh
docker ps            # запущенные контейнеры
docker ps -a         # все контейнеры
docker ps -aq        # id всех контейнеров

docker inspect [container-name]   # подробная информация о контейнере
docker logs container-id          # логи (`-f` follow, `--tail`)

docker rm [container-name]        # удалить контейнер
docker rm -fv $(docker ps -aq)    # удалить все контейнеры (force + volumes)

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

docker attach [container-name]    # подключиться к STDIN/STDOUT
docker exec -it container bash    # запустить процесс внутри контейнера
docker top container-name         # процессы контейнера
docker diff container-name        # изменения файловой системы
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
* `-v host:container`: mount директории
* `-e KEY=value`: env переменные

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
docker run -v /host/datadir:/var/lib/mysql:ro mysql   # bind mount (read-only)
docker run -v /var/lib/mysql mysql                    # anonymous volume
docker run -v mysql_data:/var/lib/mysql mysql         # named volume
```

Anonymous volume создается автоматически и хранится в `/var/lib/docker/volumes/...`

---

## Networks

```shell
docker network ls
docker network create mynet
docker network inspect mynet
docker network rm mynet

docker run --network mynet nginx
```

---

## Other

```shell
docker container prune
docker image prune
docker volume prune
docker network prune
docker system df        # сколько места занимает docker
```
