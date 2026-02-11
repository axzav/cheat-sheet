202302272055
Tags: #
____________________________________________________
# OOP

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
    use Hello, World; //трейты

    public const BAR = 'bar'; //constant visibility modifiers >= 7.1

    public readonly string $prop; // readonly property >= 8.1

    public string  $var1 = 'default value'; // property type >= 7.4
    protected      $var2;
    private        $var3;

    public static $staticVar = 'foo';

    //в констукторе могут быть сразу указаны поля класса для автоматической инициализации >= 8.0
    public function __construct(protected int $x, protected int $y = 0) {
    
    }

    // объявление метода
    public function displayVar() {
        echo $this->var;
    }

    // возможно объявление свойства и метода с одинаковым именем
    public function var1() {
        return $this->var1;
    }

    public function staticValue() {
        return self::$staticVar;
    }
}
```

------------


Динамические свойства устарели, начиная с PHP 8.2.0. Вместо этого рекомендуется объявлять свойство. Для работы с произвольными именами свойств, класс должен реализовать магические методы [__get()](https://www.php.net/manual/ru/language.oop5.overloading.php#object.get) и [__set()](https://www.php.net/manual/ru/language.oop5.overloading.php#object.set). В крайнем случае, класс можно пометить атрибутом `#[\AllowDynamicProperties]`.


Если не используется автозагрузка, классы должны быть объявлены до того, как они будут использоваться. Если класс расширяет другой, то родительский класс должен быть объявлен до наследующего класса. Это правило применяется к классам, которые наследуют другие классы или интерфейсы.


- динамические свойства
- final - не может быть переопределено в дочернем классе https://www.php.net/manual/ru/language.oop5.final.php
- readonly class >= 8.2
- new static - static late binding
- множественное наследование запрещено, но можно сделать цепочку наследований
- Интерфейсы разделяют пространство имён с классами и трейтами, поэтому они не могут называться одинаково.
- итераторы объектов - через foreach можно перебрать все публичные свойства объекта
- клонирование объектов - https://www.php.net/manual/ru/language.oop5.cloning.php
- сравнение объектов -  https://www.php.net/manual/ru/language.oop5.object-comparison.php
- позднее статическое связывание - https://www.php.net/manual/ru/language.oop5.late-static-bindings.php
- передача объектов по ссылке - https://www.php.net/manual/ru/language.oop5.references.php
- ковариантность, контрвариантность  https://www.php.net/manual/ru/language.oop5.variance.php

____________________________________________________
# Base category
- 
____________________________________________________
# Links
- 