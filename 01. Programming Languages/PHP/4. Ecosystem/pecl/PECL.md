# PECL

PECL (PHP Extension Community Library) — репозиторий расширений PHP, написанных на C.

## Установка PECL

### Linux

```bash
# Ubuntu/Debian
apt-get install php-pear php-dev
pecl install extension-name

# CentOS/RHEL
yum install php-pear php-devel
pecl install extension-name
```

### macOS

```bash
# С Homebrew
brew install php
pecl install extension-name
```

### Docker

```dockerfile
FROM php:8.1-fpm
RUN pecl install extension-name && docker-php-ext-enable extension-name
```

## Основные команды

```bash
# Установка расширения
pecl install extension-name

# Удаление расширения
pecl uninstall extension-name

# Обновление расширения
pecl upgrade extension-name

# Список установленных расширений
pecl list

# Поиск расширений
pecl search keyword

# Информация о расширении
pecl info extension-name

# Список доступных каналов
pecl list-channels
```

## Популярные расширения

### Xdebug

```bash
pecl install xdebug
```

### Redis

```bash
pecl install redis
```

### MongoDB

```bash
pecl install mongodb
```

### APCu

```bash
pecl install apcu
```

### Imagick

```bash
pecl install imagick
```

### Memcached

```bash
pecl install memcached
```

## Конфигурация после установки

После установки расширения нужно добавить его в `php.ini`:

```ini
extension=extension-name.so
```

Или создать отдельный файл конфигурации:

```bash
echo "extension=extension-name.so" > /etc/php/8.1/mods-available/extension-name.ini
php8enmod extension-name
```

## Установка с опциями

```bash
# С дополнительными опциями
pecl install --configureoptions '--with-option=value' extension-name

# Без зависимостей (не рекомендуется)
pecl install --nodeps extension-name

# Только загрузка, без установки
pecl download extension-name
```

## Проблемы и решения

### Проблемы с правами

```bash
# Установка в системную директорию
sudo pecl install extension-name

# Или установка в пользовательскую директорию
pecl install -d /path/to/pear extension-name
```

### Проблемы с зависимостями

```bash
# Установка зависимостей вручную
apt-get install lib-dev-package
pecl install extension-name
```

### Проблемы с компиляцией

```bash
# Проверка логов
pecl install extension-name 2>&1 | tee install.log

# Установка с отладочной информацией
pecl install -v extension-name
```

## Альтернативы PECL

### Docker PHP extensions

```dockerfile
FROM php:8.1-fpm
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install gd
```

### Pickle

Альтернативный установщик расширений PHP.

```bash
composer require --dev friendsofphp/pickle
vendor/bin/pickle install extension-name
```

## Ссылки

- https://pecl.php.net/
- https://github.com/FriendsOfPHP/pickle
