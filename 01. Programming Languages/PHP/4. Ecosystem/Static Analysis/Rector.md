# Rector

Rector — инструмент для автоматического рефакторинга и миграции PHP кода, обновления версий PHP и применения best practices.

## Установка

```bash
composer require --dev rector/rector
vendor/bin/rector init
```

## Базовое использование

```bash
# Показать что будет изменено (dry-run)
vendor/bin/rector process src/ --dry-run

# Применить изменения
vendor/bin/rector process src/

# Применить конкретное правило
vendor/bin/rector process src/ --set php74
```

## Конфигурация (rector.php)

```php
<?php

use Rector\Config\RectorConfig;
use Rector\Set\ValueObject\LevelSetList;
use Rector\Set\ValueObject\SetList;

return RectorConfig::configure()
    ->withPaths([
        __DIR__ . '/src',
    ])
    ->withSets([
        LevelSetList::UP_TO_PHP_81,
        SetList::CODE_QUALITY,
        SetList::DEAD_CODE,
        SetList::EARLY_RETURN,
    ])
    ->withSkip([
        __DIR__ . '/src/Legacy',
    ]);
```

## Наборы правил (Sets)

### Обновление версии PHP

```php
use Rector\Set\ValueObject\LevelSetList;

->withSets([
    LevelSetList::UP_TO_PHP_74,
    LevelSetList::UP_TO_PHP_80,
    LevelSetList::UP_TO_PHP_81,
    LevelSetList::UP_TO_PHP_82,
])
```

### Качество кода

```php
use Rector\Set\ValueObject\SetList;

->withSets([
    SetList::CODE_QUALITY,
    SetList::DEAD_CODE,
    SetList::EARLY_RETURN,
    SetList::TYPE_DECLARATION,
])
```

### Фреймворки

```php
use Rector\Symfony\Set\SymfonySetList;

->withSets([
    SymfonySetList::SYMFONY_60,
    SymfonySetList::SYMFONY_CODE_QUALITY,
])
```

## Примеры правил

### Конвертация в строгую типизацию

```php
// До
function process($data) {
    return $data;
}

// После
function process(mixed $data): mixed {
    return $data;
}
```

### Ранний возврат

```php
// До
if ($condition) {
    return true;
} else {
    return false;
}

// После
if ($condition) {
    return true;
}
return false;
```

### Удаление неиспользуемого кода

```php
// Удаляет неиспользуемые методы, свойства, переменные
```

## Кастомные правила

```php
<?php

use PhpParser\Node;
use Rector\Rector\AbstractRector;
use Symplify\RuleDocGenerator\ValueObject\RuleDefinition;

final class MyCustomRector extends AbstractRector
{
    public function getNodeTypes(): array
    {
        return [Node\Expr\MethodCall::class];
    }

    public function refactor(Node $node): ?Node
    {
        // Логика рефакторинга
        return $node;
    }
}
```

## Исключения

```php
->withSkip([
    // По пути
    __DIR__ . '/src/Legacy',
    
    // По правилу
    Rector\CodeQuality\Rector\If_\SimplifyIfReturnBoolRector::class,
    
    // По паттерну
    '*Rector\DeadCode\*',
])
```

## Интеграция с CI/CD

```yaml
# .github/workflows/rector.yml
- name: Rector
  run: vendor/bin/rector process src/ --dry-run --no-progress-bar
```

## Миграция между версиями PHP

```bash
# Миграция на PHP 8.0
vendor/bin/rector process src/ --set php80

# Миграция на PHP 8.1
vendor/bin/rector process src/ --set php81
```

## Ссылки

- https://getrector.com/
- https://github.com/rectorphp/rector
