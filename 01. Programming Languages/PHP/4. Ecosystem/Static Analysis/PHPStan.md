# PHPStan

PHPStan — статический анализатор кода для PHP, который находит ошибки без запуска кода.

## Установка

```bash
composer require --dev phpstan/phpstan
```

## Базовое использование

```bash
# Анализ директории
vendor/bin/phpstan analyse src/

# С указанием уровня (0-9)
vendor/bin/phpstan analyse --level=5 src/

# С выводом ошибок
vendor/bin/phpstan analyse --level=5 src/ --error-format=table
```

## Уровни строгости

- **Level 0** — базовая проверка
- **Level 1-3** — общие проверки
- **Level 4-6** — строгие проверки типов
- **Level 7-9** — максимальная строгость

## Конфигурация (phpstan.neon)

```yaml
parameters:
    level: 5
    paths:
        - src/
    excludePaths:
        - src/legacy/
    ignoreErrors:
        - '#Call to an undefined method#'
    checkMissingIterableValueType: false
```

## Правила (Rules)

```yaml
parameters:
    rules:
        - PHPStan\Rules\Functions\CallToFunctionParametersPassedByReferenceRule
        - PHPStan\Rules\Methods\CallMethodsRule
```

## Расширения

### PHPUnit

```bash
composer require --dev phpstan/phpstan-phpunit
```

```yaml
includes:
    - vendor/phpstan/phpstan-phpunit/extension.neon
```

### Symfony

```bash
composer require --dev phpstan/phpstan-symfony
```

```yaml
includes:
    - vendor/phpstan/phpstan-symfony/extension.neon
```

### Doctrine

```bash
composer require --dev phpstan/phpstan-doctrine
```

```yaml
includes:
    - vendor/phpstan/phpstan-doctrine/extension.neon
```

### Laravel

```bash
composer require --dev larastan/larastan
```

## PHPDoc аннотации

```php
<?php
/**
 * @param string $name
 * @param int $age
 * @return User
 */
function createUser(string $name, int $age): User
{
    // ...
}

/**
 * @var array<int, User>
 */
$users = [];

/**
 * @template T
 * @param T $value
 * @return T
 */
function identity($value) {
    return $value;
}
```

## Игнорирование ошибок

```php
<?php
// Игнорировать следующую строку
/** @phpstan-ignore-next-line */
$result = $undefined->method();

// Игнорировать конкретную ошибку
/** @phpstan-ignore variable.undefined */
$result = $undefined;
```

## Baseline

```bash
# Создать baseline из существующих ошибок
vendor/bin/phpstan analyse --generate-baseline

# Использовать baseline
vendor/bin/phpstan analyse --level=8 --configuration=phpstan-baseline.neon
```

## Интеграция с CI/CD

```yaml
# .github/workflows/phpstan.yml
- name: PHPStan
  run: vendor/bin/phpstan analyse --level=5 src/
```

## Ссылки

- https://phpstan.org/
- https://github.com/phpstan/phpstan
