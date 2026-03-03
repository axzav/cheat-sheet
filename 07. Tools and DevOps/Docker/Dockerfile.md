# Dockerfile

**Dockerfile** — файл с инструкциями для создания Docker образа.

```dockerfile
FROM ubuntu:18.04

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

EXPOSE 8000

ENV PYTHONUNBUFFERED=1

ENTRYPOINT ["python"]
CMD ["app.py"]
```

**Основные инструкции Dockerfile:**
- `FROM` — базовый образ
- `LABEL` — метаданные образа
- `WORKDIR` — рабочая директория
- `COPY` / `ADD` — копирование файлов
- `RUN` — выполнение команд при сборке
- `ENV` — переменные окружения
- `ARG` — аргументы сборки
- `EXPOSE` — документирование портов
- `VOLUME` — точки монтирования
- `USER` — пользователь для команд
- `ENTRYPOINT` / `CMD` — команда запуска контейнера


## ENTRYPOINT and CMD

```dockerfile
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```

```shell
nginx -g "daemon off;"  # ENTRYPOINT + CMD
```

* **ENTRYPOINT** - основной процесс контейнера
* **CMD** - аргументы запуска основного процесса

**Важно:** Если в `Dockerfile` не указаны `CMD` и `ENTRYPOINT`, контейнер сразу завершится с ошибкой "no command specified".

**CMD** можно перезаписать при запуске контейнера, например:
```shell
ENTRYPOINT ["echo"]
CMD ["Hello Docker"]
```

```shell
docker run test_img            # Выведет "Hello Docker"
docker run test_img hello_run  # Выведет "hello_run"
```

**ENTRYPOINT** можно переопределить при запуске контейнера с флагом `--entrypoint`:
```shell
docker run --entrypoint bash nginx
```

**ENTRYPOINT + CMD = лучший паттерн**. Позволяет делать такие вещи:
```dockerfile
ENTRYPOINT ["node"]
CMD ["app.js"]
```

```shell
docker run image            # default: node app.js
docker run image other.js   # node other.js
```

**Антипаттерн:**
```dockerfile
ENTRYPOINT ["node", "app.js"]
```


## RUN

**RUN** — выполнить команду во время сборки образа. Каждая инструкция RUN создает новый слой.

```dockerfile
# Каждая команда в отдельном слое (не рекомендуется)
RUN apt-get update
RUN apt-get install -y nginx

# Объединение команд (рекомендуется)
RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

**Best practices:**
- Объединять связанные команды в один RUN
- Очищать кеш пакетных менеджеров в том же слое
- Использовать `&&` для цепочки команд


## COPY

**COPY** — копирует файлы с хоста в образ. Поддерживает кеширование слоев.

```dockerfile
COPY source dest
COPY source1 source2 dest/
COPY --chown=user:group source dest
```

**Best practices для кеша:**

Копировать файлы зависимостей перед копированием кода, чтобы использовать кеш слоев:

```dockerfile
# Плохо: при изменении любого файла переустанавливаются зависимости
COPY . .
RUN composer install

# Хорошо: зависимости кешируются, если не изменились composer.json/lock
COPY composer.json composer.lock ./
RUN composer install --no-dev
COPY . .
```

**COPY vs ADD:**
- `COPY` — простое копирование файлов (рекомендуется)
- `ADD` — копирование + распаковка архивов + загрузка из URL (только при необходимости)


## WORKDIR

**WORKDIR** — устанавливает рабочую директорию для последующих инструкций.

```dockerfile
WORKDIR /app
COPY . .              # Копирует в /app
RUN npm install       # Выполняется в /app
```

**Особенности:**
- Создает директорию, если она не существует
- Все последующие команды выполняются в этой директории
- При подключении к контейнеру терминал открывается в WORKDIR
- Можно использовать несколько раз для перехода в разные директории


## EXPOSE

**EXPOSE** — документирует порты, которые контейнер будет слушать. Не открывает порты автоматически

```dockerfile
EXPOSE 80
EXPOSE 80/tcp
EXPOSE 80/udp
EXPOSE 3000 8080
```

**Важно:**
- `EXPOSE` — только документация, не открывает порты
- Для открытия портов - `docker run -p` или `ports:` в docker-compose
- Полезно для документации и автоматической настройки в docker-compose


## ENV

**ENV** — устанавливает переменные окружения в образе.

```dockerfile
ENV NODE_ENV=production
ENV APP_NAME="My App"
ENV PATH=/usr/local/bin:$PATH
```

**Приоритет переменных окружения:**
1. `docker run -e KEY=value` (высший приоритет)
2. `ENV` в Dockerfile
3. Значения из базового образа

**Важно:** Переменные из `docker run -e` доступны только во время выполнения контейнера, не во время сборки. Для сборки - `ARG`.

**Формат:**
```dockerfile
ENV KEY=value           # Одна переменная
ENV KEY1=value1 KEY2=value2  # Несколько переменных
```

## VOLUME

**VOLUME** — создает точку монтирования для данных, которые должны сохраняться вне контейнера.

```dockerfile
VOLUME ["/var/lib/mysql", "/data"]
VOLUME /var/log
```

**Важно:**
- `VOLUME` в Dockerfile создает anonymous volume при запуске контейнера
- Данные в VOLUME сохраняются при удалении контейнера (если не использовать `docker rm -v`)
- В docker-compose лучше использовать `volumes:` для явного управления томами
- Для разработки надо использовать bind mount в docker-compose вместо VOLUME в Dockerfile

**Рекомендация:** Обычно лучше не использовать VOLUME в Dockerfile, а определять volumes в docker-compose.yml для гибкости.


## ARG

**ARG** — аргументы сборки, доступны только во время сборки образа.

```dockerfile
ARG NODE_VERSION=18
ARG BUILD_DATE
FROM node:${NODE_VERSION}
```

**Использование:**
```sh
docker build --build-arg NODE_VERSION=20 .
docker build --build-arg BUILD_DATE=$(date) .
```

**ARG vs ENV:**
- `ARG` — доступен только при сборке, не сохраняется в образе
- `ENV` — доступен при сборке и в контейнере, сохраняется в образе
- `ARG` можно использовать для передачи секретов при сборке без их сохранения в образе

**Best practices:**
- Использовать `ARG` для версий, путей сборки
- Не использовать `ARG` для секретов (лучше использовать BuildKit secrets)

## USER

**USER** — указывает пользователя для последующих инструкций.

**USER** устанавливает пользователя для всех последующих инструкций в Dockerfile:
* Во время сборки образа — влияет на `RUN` после `USER`
* При запуске контейнера — влияет на `CMD` и `ENTRYPOINT`

Процесс php-fpm запускается от пользователя `appuser`, а не от `root`:
```dockerfile
FROM php:8.2-fpm

# Все команды до USER выполняются от root
RUN apt-get update && apt-get install -y git
RUN groupadd -g 1000 appuser
RUN useradd -u 1000 -g 1000 appuser

# После USER все команды выполняются от appuser
USER appuser

# выполнится от appuser
RUN composer install

# CMD тоже выполнится от appuser
CMD ["php-fpm"]
```

USER можно переопределить при запуске:
```shell
docker run --user root my-php-image  # Запустит от root, игнорируя USER из Dockerfile
```

**Важно:**
- По умолчанию команды выполняются от root
- Для безопасности запускать приложение от непривилегированного пользователя
- Убедиться, что пользователь существует (создайте его в RUN)

## LABEL

**LABEL** — добавляет метаданные к образу.

```dockerfile
LABEL maintainer="your-email@example.com"
LABEL version="1.0"
LABEL description="My application"
LABEL org.opencontainers.image.version="1.0"
```

## ADD

**ADD** — копирование файлов с дополнительными возможностями (не рекомендуется).

```dockerfile
ADD source dest
ADD archive.tar.gz /app  # Автоматически распаковывает
ADD https://example.com/file /app  # Загружает из URL
```

**Рекомендация:** `COPY` вместо `ADD`, если не нужны специальные возможности.

## Multi-stage build

Использование нескольких стадий сборки для уменьшения размера образа:

```dockerfile
# Stage 1: Build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm install --production
CMD ["node", "dist/index.js"]
```

## Best practices

1. **Использовать .dockerignore** — исключайте ненужные файлы из контекста сборки
2. **Минимизация слоев** — объединяйте команды в один RUN
3. **Использовать официальные базовые образы**
4. **Фиксировать версии** — не использовать `latest`
5. **Не запускать от root** — использовать USER
6. **Копировать зависимости перед кодом** — для лучшего кеширования
7. **Очищать кеш** — удалять временные файлы в том же слое
8. **Использовать multi-stage build** — для уменьшения размера образа
