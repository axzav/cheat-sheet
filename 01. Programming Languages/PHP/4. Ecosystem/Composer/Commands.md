# Composer Commands

## Установка и обновление

```bash
# Установка зависимостей
composer install

# Установка с увеличенным лимитом памяти
COMPOSER_MEMORY_LIMIT=-1 composer install

# Обновление зависимостей
composer update

# Обновление конкретного пакета
composer update vendor/package

# Обновление без dev-зависимостей
composer update --no-dev
```

## Добавление и удаление пакетов

```bash
# Добавить пакет
composer require vendor/package

# Добавить пакет с версией
composer require vendor/package:^2.0

# Добавить dev-зависимость
composer require --dev vendor/package

# Удалить пакет
composer remove vendor/package

# Проверка перед установкой (dry-run)
composer require vendor/package:* --dry-run
```

## Просмотр информации

```bash
# Список всех установленных пакетов
composer show

# Пакеты по паттерну
composer show "symfony/*"

# Информация о конкретном пакете
composer show symfony/cache

# Полная информация о пакете
composer show symfony/cache --all

# Версии пакета
composer show guzzlehttp/guzzle --all | grep versions
```

## Анализ зависимостей

```bash
# Какие пакеты требуют указанный пакет
composer why symfony/error-handler

# Почему установлен пакет (обратная зависимость)
composer why-not vendor/package

# Дерево зависимостей
composer depends vendor/package

# Обратные зависимости
composer why vendor/package
```

## Автозагрузка

```bash
# Обновить автозагрузку
composer dump-autoload

# Оптимизированная автозагрузка (production)
composer dump-autoload --optimize

# Автозагрузка с авторизацией классов
composer dump-autoload --classmap-authoritative
```

## Валидация и проверка

```bash
# Валидация composer.json
composer validate

# Проверка устаревших пакетов
composer outdated

# Проверка безопасности
composer audit
```

## Скрипты

```bash
# Запуск скриптов из composer.json
composer run-script script-name

# Список доступных скриптов
composer run-script --list
```

## Кэш

```bash
# Очистить кэш
composer clear-cache

# Показать информацию о кэше
composer clear-cache --verbose
```

## Другие полезные команды

```bash
# Статус зависимостей
composer status

# Лицензии установленных пакетов
composer licenses

# Информация о версии Composer
composer --version

# Справка по команде
composer help install
```
