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

### Основные изменения от PSR-2

- Строки могут быть длиннее 120 символов, но не должны быть длиннее
- Открывающая фигурная скобка должна быть на той же строке
- Ключевое слово `elseif` должно использоваться вместо `else if`
- Все свойства должны иметь объявленную видимость

## PSR-13: Hypermedia Links

Стандартный способ представления гипермедиа ссылок.

```php
use Psr\Link\LinkInterface;

$link = new Link('self', '/api/users/1');
```

## PSR-14: Event Dispatcher

Стандартные интерфейсы для диспетчеризации событий.

```php
use Psr\EventDispatcher\EventDispatcherInterface;

$dispatcher->dispatch(new UserCreatedEvent($user));
```

## PSR-15: HTTP Server Request Handlers

Стандартные интерфейсы для HTTP серверных обработчиков запросов.

```php
use Psr\Http\Server\RequestHandlerInterface;
use Psr\Http\Server\MiddlewareInterface;
```

## PSR-16: Simple Cache

Упрощенный интерфейс кэширования.

```php
use Psr\SimpleCache\CacheInterface;

$cache->set('key', 'value', 3600);
$value = $cache->get('key');
$cache->delete('key');
```

## PSR-17: HTTP Factories

Фабрики для создания HTTP сообщений.

```php
use Psr\Http\Message\RequestFactoryInterface;
use Psr\Http\Message\ResponseFactoryInterface;

$request = $requestFactory->createRequest('GET', '/api/users');
$response = $responseFactory->createResponse(200);
```

## PSR-18: HTTP Client

Стандартный интерфейс для HTTP клиентов.

```php
use Psr\Http\Client\ClientInterface;

$request = $requestFactory->createRequest('GET', 'https://api.example.com');
$response = $client->sendRequest($request);
```

## Использование

Большинство современных PHP фреймворков и библиотек следуют PSR стандартам, что обеспечивает их совместимость и возможность совместного использования.

## Ссылки

- https://www.php-fig.org/psr/
- https://github.com/php-fig
- https://elisdn.ru/blog/134/four-pillars-of-psrs - 4 столпа PSR
