# PHP Code Sniffer

PHP_CodeSniffer — инструмент для проверки соответствия кода стандартам кодирования (PSR-1, PSR-2, PSR-12 и др.).

## Установка

```bash
composer require --dev squizlabs/php_codesniffer
```

## Использование

```bash
# Проверить файл или директорию
php vendor/bin/phpcs src/

# Проверить с автоматическим исправлением
php vendor/bin/phpcbf src/

# Указать стандарт
php vendor/bin/phpcs --standard=PSR12 src/

# Показать только ошибки (без предупреждений)
php vendor/bin/phpcs --error-severity=1 src/
```

## Стандарты

```bash
# Список доступных стандартов
php vendor/bin/phpcs -i

# Использовать конкретный стандарт
php vendor/bin/phpcs --standard=PSR12 src/
php vendor/bin/phpcs --standard=PSR2 src/
php vendor/bin/phpcs --standard=PSR1 src/
```

## Конфигурация (phpcs.xml)

```xml
<?xml version="1.0"?>
<ruleset name="MyProject">
    <description>My project coding standards</description>
    
    <!-- Путь к проверяемым файлам -->
    <file>src/</file>
    <file>tests/</file>
    
    <!-- Исключения -->
    <exclude-pattern>*/vendor/*</exclude-pattern>
    <exclude-pattern>*/cache/*</exclude-pattern>
    
    <!-- Стандарт -->
    <rule ref="PSR12"/>
    
    <!-- Игнорировать конкретные правила -->
    <rule ref="PSR12.Properties.ConstantVisibility">
        <severity>0</severity>
    </rule>
</ruleset>
```

## Интеграция с Composer

```json
{
    "scripts": {
        "cs-check": "phpcs --standard=PSR12 src/",
        "cs-fix": "phpcbf --standard=PSR12 src/"
    }
}
```

```bash
composer cs-check
composer cs-fix
```

## Кастомные правила

```bash
# Создать свой стандарт
php vendor/bin/phpcs --config-set installed_paths /path/to/standards
```

## Ссылки

- https://github.com/squizlabs/PHP_CodeSniffer
- https://github.com/squizlabs/PHP_CodeSniffer/wiki
