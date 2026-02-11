202304152218
Tags: #
____________________________________________________
# Autoloading

Механизм автозагрузки позволяет подключать файлы классов по мере их использования.

До появления автозагрузки использовалось включение файлов с помощью `include`, `include_once`, `require`, или `require_once`. Это усложняло структуру зависимостей.

Для автозагрузки используется функция `spl_autoload_register()`, в которую передается название функции, которая описывает логику загрузки файлов классов на основе полного квалифицированного имени класса.

Стандарт PSR-4 (https://www.php-fig.org/psr/psr-4/) указывает на то, как должна быть организована файловая структура для автозагрузки.

Простая реализация стандарта может выглядеть так:

```css
project-root/
  |- src/
      |- MyProject/
          |- MyClass.php
  |- index.php
```

Реализация функции автозагрузки:

```php
// index.php

function psr4Autoloader($className) {
    // Define the base directory for the namespace prefix
    $baseDir = __DIR__ . '/src/';

    // Replace the namespace prefix with the base directory and replace namespace separators with directory separators
    $file = $baseDir . str_replace('\\', '/', $className) . '.php';

    // If the file exists, require it
    if (file_exists($file)) {
        require $file;
    }
}

// Register the autoloader
spl_autoload_register('psr4Autoloader');
```

Далее классы из директории `src`  будут подгружены по мере использования:

```php
// index.php (continued)

use MyProject\MyClass;

$myClass = new MyClass();
echo $myClass->hello(); // Output: Hello, world!
```

## composer

В композере идет поддержка PSR-4 стандарта автозагрузки. Чтобы использовать ее нужно в `composer.json` указать базовую директорию классов для автозагрузки и namespace prefix:

```json
{
    "autoload": {
        "psr-4": {
            "MyProject\\": "src/"
        }
    }
}
```

В точке входа нужно подключить файл с реализацией автозагрузки композера:

```php
require_once 'vendor/autoload.php';
```

____________________________________________________
# Base category
- 
____________________________________________________
# Links
- 