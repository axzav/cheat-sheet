# Composer Tools

Инструменты для работы с Composer и визуализации зависимостей.

## Визуализация зависимостей

### Composer Graph

```bash
composer require --dev jbzoo/composer-graph
composer graph
```

- https://github.com/JBZoo/Composer-Graph

### Graph Composer

```bash
composer require --dev clue/graph-composer
vendor/bin/graph-composer graph
```

- https://github.com/clue/graph-composer
- https://clue.engineering/2020/introducing-graph-composer

## Анализ зависимостей

### Composer Normalize

Нормализация `composer.json` файла.

```bash
composer require --dev ergebnis/composer-normalize
composer normalize
```

- https://github.com/ergebnis/composer-normalize

### Composer Unused

Поиск неиспользуемых зависимостей.

```bash
composer require --dev icanhazstring/composer-unused
composer unused
```

- https://github.com/icanhazstring/composer-unused

## Безопасность

### Composer Audit

Встроенная проверка безопасности (Composer 2.4+).

```bash
composer audit
composer audit --format=json
```

### SensioLabs Security Checker

```bash
composer require --dev sensiolabs/security-checker
vendor/bin/security-checker security:check
```

- https://github.com/sensiolabs/security-checker

## Производительность

### Prestissimo (устарел, встроен в Composer 2)

Ускорение загрузки пакетов через параллельные загрузки.

- Composer 2 имеет встроенную параллельную загрузку

### Composer Preload

Предзагрузка классов для улучшения производительности.

```bash
composer require --dev krakjoe/composer-preload
composer preload
```

- https://github.com/krakjoe/composer-preload

## Другие инструменты

### Composer Patches

Применение патчей к зависимостям.

```bash
composer require --dev cweagans/composer-patches
```

- https://github.com/cweagans/composer-patches

### Composer Merge Plugin

Объединение нескольких `composer.json` файлов.

```bash
composer require --dev wikimedia/composer-merge-plugin
```

- https://github.com/wikimedia/composer-merge-plugin

## Ссылки

- https://blog.jetbrains.com/phpstorm/2013/10/visualizing-composer-dependencies-with-file-watchers/
