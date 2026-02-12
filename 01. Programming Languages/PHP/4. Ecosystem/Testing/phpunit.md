# PHPUnit

PHPUnit — фреймворк для модульного тестирования PHP приложений.

## Установка

```bash
composer require --dev phpunit/phpunit
```

## Базовое использование

```bash
# Запуск всех тестов
php vendor/bin/phpunit

# Запуск конкретного теста
php vendor/bin/phpunit tests/UserTest.php

# Запуск тестов из директории
php vendor/bin/phpunit tests/

# Фильтр по имени теста
php vendor/bin/phpunit --filter testIsValid

# Фильтр по классу
php vendor/bin/phpunit --filter UserTest
```

## Структура теста

```php
<?php
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase
{
    protected function setUp(): void
    {
        // Подготовка перед каждым тестом
    }

    protected function tearDown(): void
    {
        // Очистка после каждого теста
    }

    public function testUserCreation(): void
    {
        $user = new User('John');
        $this->assertEquals('John', $user->getName());
    }
}
```

## Утверждения (Assertions)

### Базовые утверждения

```php
<?php
// Равенство
$this->assertEquals($expected, $actual);
$this->assertSame($expected, $actual); // Строгое сравнение

// Истинность
$this->assertTrue($condition);
$this->assertFalse($condition);

// Null
$this->assertNull($value);
$this->assertNotNull($value);

// Типы
$this->assertInstanceOf(ExpectedClass::class, $object);
$this->assertIsArray($value);
$this->assertIsString($value);
```

### Массивы и коллекции

```php
<?php
// Содержит
$this->assertContains($needle, $haystack);
$this->assertNotContains($needle, $haystack);

// Ключ существует
$this->assertArrayHasKey('key', $array);
$this->assertArrayNotHasKey('key', $array);

// Подмножество
$this->assertArraySubset($subset, $array);
```

### Исключения

```php
<?php
// Ожидание исключения
$this->expectException(InvalidArgumentException::class);
$this->expectExceptionMessage('Error message');
$this->expectExceptionCode(500);

// Или через аннотацию
/**
 * @expectedException InvalidArgumentException
 * @expectedExceptionMessage Error message
 */
public function testException(): void
{
    // код, который выбрасывает исключение
}
```

## Покрытие кода (Code Coverage)

### Настройка

Требуется Xdebug с режимом coverage:

```ini
xdebug.mode=coverage
```

### Генерация отчета

```bash
# HTML отчет
php vendor/bin/phpunit --coverage-html coverage-report

# С переменной окружения
XDEBUG_MODE=coverage php vendor/bin/phpunit --coverage-html coverage-report

# Текстовый отчет
php vendor/bin/phpunit --coverage-text

# Clover XML (для CI)
php vendor/bin/phpunit --coverage-clover coverage.xml
```

### Минимальное покрытие

```bash
php vendor/bin/phpunit --coverage-html coverage-report --coverage-text --coverage-filter=src --min-coverage=80
```

## Конфигурация (phpunit.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/9.5/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
         verbose="true">
    <testsuites>
        <testsuite name="Unit">
            <directory>tests/Unit</directory>
        </testsuite>
        <testsuite name="Integration">
            <directory>tests/Integration</directory>
        </testsuite>
    </testsuites>
    
    <coverage>
        <include>
            <directory suffix=".php">src</directory>
        </include>
        <exclude>
            <directory>vendor</directory>
        </exclude>
    </coverage>
    
    <php>
        <env name="APP_ENV" value="testing"/>
        <ini name="memory_limit" value="512M"/>
    </php>
</phpunit>
```

## Data Providers

```php
<?php
/**
 * @dataProvider userDataProvider
 */
public function testUserCreation($name, $expected): void
{
    $user = new User($name);
    $this->assertEquals($expected, $user->getName());
}

public function userDataProvider(): array
{
    return [
        'John' => ['John', 'John'],
        'Jane' => ['Jane', 'Jane'],
    ];
}
```

## Моки и стабы

```php
<?php
// Создание мока
$mock = $this->createMock(Repository::class);
$mock->method('find')->willReturn(new User());

// Создание стаба
$stub = $this->createStub(Repository::class);
$stub->method('find')->willReturn(new User());

// getMockBuilder для сложных случаев
$mock = $this->getMockBuilder(Repository::class)
    ->setConstructorArgs([$dependency])
    ->onlyMethods(['find', 'save'])
    ->getMock();
```

## Отладка с Xdebug

```bash
# Запуск с Xdebug
XDEBUG_TRIGGER=1 php vendor/bin/phpunit --filter testName

# Или через переменную окружения
XDEBUG_SESSION_START=1 php vendor/bin/phpunit
```

## Параллельное выполнение

```bash
# Установка
composer require --dev phpunit/phpunit

# Запуск параллельно
php vendor/bin/phpunit --process-isolation
```

## Ссылки

- https://phpunit.de/
- https://github.com/sebastianbergmann/phpunit
