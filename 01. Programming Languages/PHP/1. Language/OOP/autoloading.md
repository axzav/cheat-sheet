# Автозагрузка классов

Механизм автозагрузки позволяет подключать файлы классов автоматически при их использовании.

## До автозагрузки

Раньше использовалось ручное подключение файлов:

```php
require_once 'classes/MyClass.php';
require_once 'classes/AnotherClass.php';
// ...
```

Это усложняло структуру зависимостей и требовало отслеживания всех необходимых файлов.

## spl_autoload_register

Для автозагрузки используется функция `spl_autoload_register()`, в которую передается функция, описывающая логику загрузки файлов на основе полного квалифицированного имени класса.

## PSR-4 стандарт

Стандарт PSR-4 определяет, как должна быть организована файловая структура для автозагрузки.

### Структура проекта

```
project-root/
  |- src/
      |- MyProject/
          |- MyClass.php
  |- index.php
```

### Простая реализация PSR-4

```php
function psr4Autoloader($className) {
    $baseDir = __DIR__ . '/src/';
    $file = $baseDir . str_replace('\\', '/', $className) . '.php';
    
    if (file_exists($file)) {
        require $file;
    }
}

spl_autoload_register('psr4Autoloader');
```

### Использование

```php
use MyProject\MyClass;

$myClass = new MyClass();
```

## Composer автозагрузка

Composer поддерживает PSR-4 стандарт. В `composer.json`:

```json
{
    "autoload": {
        "psr-4": {
            "MyProject\\": "src/"
        }
    }
}
```

После выполнения `composer install` или `composer dump-autoload` подключите автозагрузчик:

```php
require_once 'vendor/autoload.php';
```

## Несколько пространств имен

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "src/",
            "Tests\\": "tests/"
        }
    }
}
```

## Преимущества автозагрузки

- Не нужно вручную подключать файлы
- Классы загружаются только при использовании
- Соответствие стандартам PSR-4
- Упрощение структуры проекта
