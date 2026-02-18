# Symfony VarDumper

Symfony VarDumper — компонент для красивого отображения переменных PHP, альтернатива `var_dump()` и `print_r()`.

- Красивое форматирование
- Цветной вывод в CLI
- Интерактивный HTML вывод
- Поддержка больших объектов
- Настраиваемая глубина
- Удаленный вывод через сервер

## Установка

```bash
composer require symfony/var-dumper
```

## Базовое использование

```php
<?php
use Symfony\Component\VarDumper\VarDumper;

// Простой dump
dump($variable);

// Или через класс
VarDumper::dump($variable);
```

## Функция dump()

```php
<?php
// Можно передать несколько переменных
dump($var1, $var2, $var3);

// Встроенная функция (если используется Symfony)
dump($variable);
```

## Форматирование вывода

### HTML формат (по умолчанию в браузере)

```php
<?php
dump($variable); // Автоматически определяет формат
```

### CLI формат

```php
<?php
// В CLI автоматически использует ANSI цвета
dump($variable);
```

## Настройка вывода

```php
<?php
use Symfony\Component\VarDumper\Cloner\VarCloner;
use Symfony\Component\VarDumper\Dumper\CliDumper;
use Symfony\Component\VarDumper\Dumper\HtmlDumper;

$cloner = new VarCloner();
$dumper = new HtmlDumper();

$dumper->dump($cloner->cloneVar($variable));
```

## Server Dumper (удаленный вывод)

### Настройка сервера

```bash
php bin/console server:dump
```

### Использование

```php
<?php
// В .env
VAR_DUMPER_FORMAT=server
VAR_DUMPER_SERVER="127.0.0.1:9912"

// В коде
dump($variable); // Выводится в сервер дампа
```

### Интеграция с Buggregator

```env
VAR_DUMPER_FORMAT=server
VAR_DUMPER_SERVER="buggregator:9912"
```

```php
<?php
dump($variable); // Отправляется в Buggregator
```

## Ограничение глубины

```php
<?php
use Symfony\Component\VarDumper\VarDumper;

VarDumper::setHandler(function ($var) {
    $cloner = new \Symfony\Component\VarDumper\Cloner\VarCloner();
    $dumper = new \Symfony\Component\VarDumper\Dumper\CliDumper();
    $dumper->setMaxDepth(3); // Ограничение глубины
    $dumper->dump($cloner->cloneVar($var));
});
```

## Фильтрация данных

```php
<?php
use Symfony\Component\VarDumper\Caster\Caster;

// Скрыть приватные свойства
$dumper->setDisplayOptions([
    'maxDepth' => 3,
    'maxStringLength' => 160,
]);
```

## Кастомные кастеры

```php
<?php
use Symfony\Component\VarDumper\Caster\Caster;
use Symfony\Component\VarDumper\Cloner\Stub;

class MyCaster
{
    public static function castMyObject($obj, array $a, Stub $stub, $isNested)
    {
        $a[Caster::PREFIX_VIRTUAL.'custom'] = 'value';
        return $a;
    }
}
```

## Ссылки

- https://symfony.com/doc/current/components/var_dumper.html
- https://github.com/symfony/var-dumper
