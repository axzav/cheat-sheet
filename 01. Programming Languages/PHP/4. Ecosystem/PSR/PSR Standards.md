# PSR стандарты

PSR (PHP Standards Recommendations) — стандарты, разработанные PHP-FIG (PHP Framework Interop Group) для обеспечения совместимости между различными PHP компонентами.

## PSR-1: Basic Coding Standard

Стандарт базового кодирования.

### Основные правила

- Файлы должны использовать только теги `<?php` и `<?=`
- Файлы должны использовать только UTF-8 без BOM
- Файлы должны либо объявлять символы (классы, функции, константы), либо выполнять побочные эффекты, но не то и другое вместе
- Пространства имен и классы должны следовать стандарту "autoloading" PSR-4
- Имена классов должны быть объявлены в `StudlyCaps`
- Константы классов должны быть объявлены только в верхнем регистре с подчеркиваниями в качестве разделителей
- Имена методов должны быть объявлены в `camelCase`

## PSR-2: Coding Style Guide

Расширение PSR-1, описывающее стиль кодирования.

### Основные правила

- Должен следовать PSR-1
- Использовать 4 пробела для отступов, не табы
- Строки не должны быть длиннее 120 символов
- После ключевого слова объявления должно быть пустой строки
- Открывающие и закрывающие фигурные скобки должны быть на отдельных строках
- Видимость свойств и методов должна быть объявлена явно

## PSR-3: Logger Interface

Стандартный интерфейс для библиотек логирования.

```php
use Psr\Log\LoggerInterface;

class MyClass
{
    private $logger;

    public function __construct(LoggerInterface $logger)
    {
        $this->logger = $logger;
    }

    public function doSomething()
    {
        $this->logger->info('Doing something');
        $this->logger->error('An error occurred', ['exception' => $e]);
    }
}
```

## PSR-4: Autoloading Standard

Стандарт автозагрузки классов.

### Структура

```
project-root/
  |- src/
      |- Vendor\
          |- Package\
              |- Class.php
```

### composer.json

```json
{
    "autoload": {
        "psr-4": {
            "Vendor\\Package\\": "src/"
        }
    }
}
```

## PSR-6: Caching Interface

Стандартный интерфейс для библиотек кэширования.

```php
use Psr\Cache\CacheItemPoolInterface;

$cache->getItem('key')->set('value');
$cache->save($item);
```

## PSR-7: HTTP Message Interface

Стандартные интерфейсы для HTTP сообщений (запросы и ответы).

```php
use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;
```

## PSR-11: Container Interface

Стандартный интерфейс для контейнеров внедрения зависимостей.

```php
use Psr\Container\ContainerInterface;

$container->get('service');
$container->has('service');
```

## PSR-12: Extended Coding Style Guide

Расширенное руководство по стилю кодирования, расширяющее PSR-2.

## PSR-15: HTTP Server Request Handlers

Стандартные интерфейсы для HTTP серверных обработчиков запросов.

## PSR-18: HTTP Client

Стандартный интерфейс для HTTP клиентов.

## Использование

Большинство современных PHP фреймворков и библиотек следуют PSR стандартам, что обеспечивает их совместимость и возможность совместного использования.
