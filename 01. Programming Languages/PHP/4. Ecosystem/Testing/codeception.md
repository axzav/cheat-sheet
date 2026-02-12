# Codeception

Codeception — полнофункциональный фреймворк для тестирования PHP приложений, поддерживающий unit, functional и acceptance тесты.

## Установка

```bash
composer require --dev codeception/codeception
php vendor/bin/codecept bootstrap
```

## Структура

```
tests/
  unit/          # Unit тесты
  functional/    # Functional тесты
  acceptance/    # Acceptance тесты
  _support/      # Вспомогательные классы
codeception.yml  # Конфигурация
```

## Конфигурация

```yaml
# codeception.yml
paths:
    tests: tests
    output: tests/_output
    support: tests/_support
    data: tests/_data
suites:
    unit:
        path: unit
        actor: UnitTester
    functional:
        path: functional
        actor: FunctionalTester
    acceptance:
        path: acceptance
        actor: AcceptanceTester
```

## Основные команды

```bash
# Создать новый тест
php vendor/bin/codecept generate:test unit MyTest

# Запустить все тесты
php vendor/bin/codecept run

# Запустить конкретный suite
php vendor/bin/codecept run unit

# Запустить конкретный тест
php vendor/bin/codecept run unit MyTest

# С покрытием кода
php vendor/bin/codecept run --coverage --coverage-html coverage
```

## Unit тесты

```php
<?php
class UserTest extends \Codeception\Test\Unit
{
    protected $tester;

    protected function _before()
    {
        // Подготовка перед каждым тестом
    }

    public function testUserCreation()
    {
        $user = new User('John');
        $this->assertEquals('John', $user->getName());
    }
}
```

## Functional тесты

```php
<?php
class LoginCest
{
    public function tryLogin(FunctionalTester $I)
    {
        $I->amOnPage('/login');
        $I->fillField('username', 'admin');
        $I->fillField('password', 'password');
        $I->click('Login');
        $I->see('Welcome');
    }
}
```

## Acceptance тесты

```php
<?php
class HomepageCest
{
    public function tryToTest(AcceptanceTester $I)
    {
        $I->amOnPage('/');
        $I->see('Welcome');
        $I->seeLink('Login', '/login');
    }
}
```

## Модули

### WebDriver (Selenium)

```yaml
modules:
    enabled:
        - WebDriver:
            url: http://localhost
            browser: chrome
```

### REST

```php
$I->sendGET('/api/users');
$I->seeResponseCodeIs(200);
$I->seeResponseIsJson();
$I->seeResponseContainsJson(['name' => 'John']);
```

### Database

```php
$I->haveInDatabase('users', [
    'name' => 'John',
    'email' => 'john@example.com'
]);
$I->seeInDatabase('users', ['name' => 'John']);
```

## Ссылки

- https://codeception.com/
- https://github.com/Codeception/Codeception
