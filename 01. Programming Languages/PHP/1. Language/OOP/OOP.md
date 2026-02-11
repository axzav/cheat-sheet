# Объектно-ориентированное программирование

## Базовый синтаксис класса

```php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World';
    }
}

class Foo extends Bar implements InterfaceA, InterfaceB
{
    use Hello, World; // Трейты

    public const BAR = 'bar'; // Видимость констант (PHP 7.1+)

    public readonly string $prop; // Readonly свойство (PHP 8.1+)

    public string $var1 = 'default value'; // Типизированное свойство (PHP 7.4+)
    protected $var2;
    private $var3;

    public static $staticVar = 'foo';

    // Продвижение свойств в конструкторе (PHP 8.0+)
    public function __construct(protected int $x, protected int $y = 0) {
    
    }

    // Объявление метода
    public function displayVar() {
        echo $this->var;
    }

    // Возможно объявление свойства и метода с одинаковым именем
    public function var1() {
        return $this->var1;
    }

    public function staticValue() {
        return self::$staticVar;
    }
}
```

## Важные особенности

### Динамические свойства

Динамические свойства устарели, начиная с PHP 8.2.0. Вместо этого рекомендуется объявлять свойство. Для работы с произвольными именами свойств, класс должен реализовать магические методы `__get()` и `__set()`. В крайнем случае, класс можно пометить атрибутом `#[\AllowDynamicProperties]`.

### Порядок объявления классов

Если не используется автозагрузка, классы должны быть объявлены до того, как они будут использованы. Если класс расширяет другой, то родительский класс должен быть объявлен до наследующего класса.

### Основные концепции

- **final** - метод или класс не может быть переопределен в дочернем классе
- **readonly class** - все свойства класса readonly (PHP 8.2+)
- **new static** - позднее статическое связывание
- **Множественное наследование** - запрещено, но можно использовать трейты
- **Интерфейсы** - разделяют пространство имён с классами и трейтами, поэтому они не могут называться одинаково
- **Итераторы объектов** - через `foreach` можно перебрать все публичные свойства объекта
- **Клонирование объектов** - использование `clone` и магического метода `__clone()`
- **Сравнение объектов** - операторы `==` и `===`
- **Позднее статическое связывание** - использование `static::` вместо `self::`
- **Передача объектов** - объекты всегда передаются по ссылке
- **Ковариантность и контрвариантность** - правила изменения типов при наследовании

## Ссылки

- https://www.php.net/manual/ru/language.oop5.final.php
- https://www.php.net/manual/ru/language.oop5.cloning.php
- https://www.php.net/manual/ru/language.oop5.object-comparison.php
- https://www.php.net/manual/ru/language.oop5.late-static-bindings.php
- https://www.php.net/manual/ru/language.oop5.references.php
- https://www.php.net/manual/ru/language.oop5.variance.php
