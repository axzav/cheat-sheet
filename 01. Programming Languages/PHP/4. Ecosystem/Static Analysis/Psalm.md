# Psalm

Psalm — статический анализатор кода для PHP от Vimeo, фокус на поиске ошибок и улучшении типизации.

## Установка

```bash
composer require --dev vimeo/psalm
vendor/bin/psalm --init
```

## Базовое использование

```bash
# Анализ проекта
vendor/bin/psalm

# С выводом информации
vendor/bin/psalm --show-info=true

# Только ошибки
vendor/bin/psalm --error-level=1
```

## Уровни строгости

```xml
<!-- psalm.xml -->
<psalm
    errorLevel="2"
    findUnusedVariablesAndParams="true"
    findUnusedPsalmSuppress="true"
/>
```

## Конфигурация (psalm.xml)

```xml
<?xml version="1.0"?>
<psalm
    errorLevel="3"
    resolveFromConfigFile="true"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="https://getpsalm.org/schema/config"
    xsi:schemaLocation="https://getpsalm.org/schema/config vendor/vimeo/psalm/config.xsd"
>
    <projectFiles>
        <directory name="src" />
        <ignoreFiles>
            <directory name="vendor" />
        </ignoreFiles>
    </projectFiles>

    <issueHandlers>
        <MissingReturnType errorLevel="error" />
        <UnusedVariable errorLevel="info" />
    </issueHandlers>
</psalm>
```

## Типизация

```php
<?php
/**
 * @psalm-param array<string, int> $data
 * @psalm-return array<string, string>
 */
function process(array $data): array {
    // ...
}

/**
 * @psalm-template T
 * @psalm-param T $value
 * @psalm-return T
 */
function identity($value) {
    return $value;
}
```

## Suppress комментарии

```php
<?php
/** @psalm-suppress PossiblyNullReference */
$result = $maybeNull->method();

/** @psalm-suppress UnusedVariable */
$unused = getValue();
```

## Плагины

### Symfony Plugin

```bash
composer require --dev psalm/plugin-symfony
vendor/bin/psalm-plugin enable psalm/plugin-symfony
```

### Doctrine Plugin

```bash
composer require --dev psalm/plugin-doctrine
vendor/bin/psalm-plugin enable psalm/plugin-doctrine
```

### Laravel Plugin

```bash
composer require --dev psalm/plugin-laravel
vendor/bin/psalm-plugin enable psalm/plugin-laravel
```

## Baseline

```bash
# Создать baseline
vendor/bin/psalm --set-baseline=psalm-baseline.xml

# Использовать baseline
vendor/bin/psalm --use-baseline=psalm-baseline.xml
```

## Автоматическое исправление

```bash
# Исправить автоматически исправимые проблемы
vendor/bin/psalm --alter --issues=MissingReturnType
```

## Интеграция с IDE

Psalm поддерживает Language Server Protocol (LSP) для интеграции с IDE.

```bash
vendor/bin/psalm --language-server
```

## Сравнение с PHPStan

- **Psalm** — больше фокуса на типизации, встроенная поддержка generics
- **PHPStan** — больше правил, лучшая поддержка плагинов
- Оба инструмента можно использовать вместе

## Ссылки

- https://psalm.dev/
- https://github.com/vimeo/psalm
