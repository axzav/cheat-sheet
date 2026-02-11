# Позднее статическое связывание

Позднее статическое связывание (Late Static Binding) позволяет использовать контекст вызывающего класса в статических методах.

## Проблема со `self::`

При использовании `self::` метод всегда ссылается на класс, где он определен:

```php
class BaseClass {
    public static function who() {
        echo "Я BaseClass\n";
    }

    public static function test() {
        self::who(); // Всегда вызывает BaseClass::who()
    }
}

class ChildClass extends BaseClass {
    public static function who() {
        echo "Я ChildClass\n";
    }
}

BaseClass::test();  // Вывод: Я BaseClass
ChildClass::test(); // Вывод: Я BaseClass (не ChildClass!)
```

В этом примере вызов `ChildClass::test()` приводит к выводу "Я BaseClass", поскольку `self::` не учитывает контекст вызывающего класса.

## Решение с `static::`

Ключевое слово `static::` позволяет переопределять статические методы в контексте наследования:

```php
class BaseClass {
    public static function who() {
        echo "Я BaseClass\n";
    }

    public static function test() {
        static::who(); // Использует контекст вызывающего класса
    }
}

class ChildClass extends BaseClass {
    public static function who() {
        echo "Я ChildClass\n";
    }
}

BaseClass::test();  // Вывод: Я BaseClass
ChildClass::test(); // Вывод: Я ChildClass ✅
```

Теперь `static::who()` корректно определяет, что должен быть вызван переопределённый метод `who()` в `ChildClass`.

## Использование в паттернах проектирования

Позднее статическое связывание особенно полезно в шаблонах проектирования, где поведение базового класса должно быть расширено или модифицировано в производных классах.

### Пример: Factory Pattern

```php
abstract class Factory {
    public static function create() {
        return new static(); // Создает экземпляр вызывающего класса
    }
}

class ProductFactory extends Factory {
    // ...
}

$product = ProductFactory::create(); // Создает ProductFactory, а не Factory
```

### Пример: Singleton Pattern

```php
abstract class Singleton {
    private static $instances = [];

    public static function getInstance() {
        $class = static::class;
        if (!isset(self::$instances[$class])) {
            self::$instances[$class] = new static();
        }
        return self::$instances[$class];
    }
}

class Database extends Singleton {
    // ...
}

$db = Database::getInstance(); // Получает экземпляр Database
```

## Разница между `self::`, `static::` и `parent::`

- `self::` - ссылается на класс, где метод определен
- `static::` - ссылается на класс, который вызвал метод (позднее связывание)
- `parent::` - ссылается на родительский класс

```php
class GrandParent {
    public static function who() {
        return 'GrandParent';
    }
}

class Parent extends GrandParent {
    public static function who() {
        return 'Parent';
    }
    
    public static function test() {
        echo self::who() . "\n";    // Parent
        echo static::who() . "\n";  // Зависит от вызывающего класса
        echo parent::who() . "\n";   // GrandParent
    }
}

class Child extends Parent {
    public static function who() {
        return 'Child';
    }
}

Child::test();
// Вывод:
// Parent
// Child
// GrandParent
```