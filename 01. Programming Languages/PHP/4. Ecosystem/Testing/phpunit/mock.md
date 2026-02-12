# PHPUnit Mocks

Создание моков и стабов в PHPUnit для изоляции тестируемого кода.

## Создание моков

### createMock()

```php
<?php
use PHPUnit\Framework\TestCase;

class UserServiceTest extends TestCase
{
    public function testGetUser(): void
    {
        // Простой мок
        $repository = $this->createMock(UserRepository::class);
        $repository->method('find')
            ->willReturn(new User('John'));
        
        $service = new UserService($repository);
        $user = $service->getUser(1);
        
        $this->assertEquals('John', $user->getName());
    }
}
```

### getMockBuilder()

Для более сложных случаев:

```php
<?php
// Мок с конструктором
$mock = $this->getMockBuilder(OAuth2::class)
    ->setConstructorArgs([
        $tokenStorage,
        $userRepositoryLocator,
        $googleClient,
        $clientRepositoryLocator,
        '',
        '',
    ])
    ->onlyMethods(['getRequiredScopes', 'validateRedirectUri'])
    ->getMock();

// Мок с частичными методами
$authenticator = $this->getMockBuilder(TotpAuthenticator::class)
    ->setConstructorArgs([$cache])
    ->onlyMethods(['isValid'])
    ->getMock();
```

## Настройка поведения методов

### willReturn()

```php
<?php
$mock->method('find')
    ->willReturn(new User('John'));

// Множественные вызовы
$mock->method('find')
    ->willReturnOnConsecutiveCalls(
        new User('John'),
        new User('Jane')
    );
```

### willThrowException()

```php
<?php
$mock->method('find')
    ->willThrowException(new NotFoundException());
```

### willReturnCallback()

```php
<?php
$mock->method('find')
    ->willReturnCallback(function ($id) {
        return new User("User{$id}");
    });
```

### willReturnSelf()

```php
<?php
$mock->method('chain')
    ->willReturnSelf();
```

## Ожидания вызовов

### expects()

```php
<?php
// Ожидается один вызов
$mock->expects($this->once())
    ->method('save')
    ->with($this->isInstanceOf(User::class))
    ->willReturn(true);

// Ожидается несколько вызовов
$mock->expects($this->exactly(2))
    ->method('update');

// Ожидается минимум один вызов
$mock->expects($this->atLeastOnce())
    ->method('notify');

// Никогда не вызывается
$mock->expects($this->never())
    ->method('delete');
```

## Проверка аргументов (with())

```php
<?php
// Точное значение
$mock->expects($this->once())
    ->method('find')
    ->with($this->equalTo(1));

// Тип
$mock->expects($this->once())
    ->method('save')
    ->with($this->isInstanceOf(User::class));

// Массив
$mock->expects($this->once())
    ->method('update')
    ->with($this->arrayHasKey('id'));

// Callback
$mock->expects($this->once())
    ->method('process')
    ->with($this->callback(function ($arg) {
        return $arg > 0;
    }));
```

## Стабы (Stubs)

### createStub()

```php
<?php
// Стаб не проверяет вызовы, только возвращает значения
$stub = $this->createStub(UserRepository::class);
$stub->method('find')
    ->willReturn(new User('John'));

// Стаб для всех методов
$stub = $this->createStub(UserRepository::class);
// Все методы возвращают null по умолчанию
```

## Частичные моки

```php
<?php
// Мокировать только некоторые методы
$mock = $this->getMockBuilder(Service::class)
    ->onlyMethods(['privateMethod', 'protectedMethod'])
    ->getMock();

// Исключить методы из мокирования
$mock = $this->getMockBuilder(Service::class)
    ->addMethods(['newMethod'])
    ->getMock();
```

## Моки интерфейсов

```php
<?php
$mock = $this->createMock(RepositoryInterface::class);
$mock->method('find')->willReturn(new User());
```

## Моки абстрактных классов

```php
<?php
$mock = $this->getMockBuilder(AbstractService::class)
    ->getMockForAbstractClass();
```

## Ссылки

- https://phpunit.de/documentation.html
