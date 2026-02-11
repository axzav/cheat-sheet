P202301141233
Tags: #
____________________________________________________
# PHP 8.x

## JIT

В пхп 8 была добавлена JIT помпиляция. Она не дает значительного прироста производительности для веб приложений, т.к. веб приложения работают с вводом-выводом, базами данных и файлами. JIT компиляция дает прирост производительности в приложениях где выполняется большой объем вычислений.

## Attributes

До пхп 8.0 фреймворки использовали PHP Doc для описания анотаций. В пхп 8.0 появился нативный способ описания анотаций. Работа с атрибутами происходит с помощью Reflection API

```php
#[ORM\Entity]
#[ORM\Table("user")]
class User
{
    #[ORM\Id, ORM\Column("integer"), ORM\GeneratedValue]
    private $id;

    #[ORM\Column("string", ORM\Column::UNIQUE)]
    #[Assert\Email(["message" => "The email '{{ value }}' is not a valid email."])]
    private $email;
}
```

## Named arguments

Именовыные аргументы позволяют вызывать методы и функции с указанием имени аргумента и указании аргументов в произвольном порядке

```php
function test($arg1, $arg2, $arg3 = 3, $arg4 = 4)
{
    return true;
}

test(1,2);
test(1,2, arg4: 5);
test(arg3: 1, arg4: 2, arg1: 3, arg2: 4);
```

## Nullsafe operator

Если один из вложенных объектов может принимать значение null тогда надо выполнять проверки для каждого элемента цепочки:

```php
$session = Session::find(123);

if ($session !== null) {
    $user = $session->user;

    if ($user !== null) {
        $address = $user->getAddress();

        if ($address !== null) {
            $country = $address->country;
        }
    }
}
```

В пхп 8.0 был добавлен оператор который проверит элемент на null и не будет выполнять дальнейшие команды если встретит null и вернет null

```php
$country = $session?->user?->getAddress()?->country;
```


## Constructor property promotion

Теперь стало возможным указывать поля класса прямо в конструкторе
```php
class Point {
  public function __construct(
    public float $x = 0.0,
    public float $y = 0.0,
    public float $z = 0.0,
  ) {}
}
```

## Union types

Раньше можно было указывать тип ожидаемого параметра только с помощью PHP Doc. В пхп 8 тайпхинтинг расширен возможностью указывать несколько типов

```php
class Number {
  public function __construct(
    private int|float $number
  ) {}
}

```

## Match expression

switch использует нестрогое стравнение

```php
switch (8.0) {
  case '8.0':
    $result = "Oh no!";
    break;
  case 8.0:
    $result = "This is what I expected";
    break;
}
echo $result;
//> Oh no!
```

В пхп 8 было введен новое выражение match, которое отличается от switch:
- строгое сравнение 
- результат может быть сохранен в переменную
- только одна строка

```php
echo match (8.0) {
  '8.0' => "Oh no!",
  8.0 => "This is what I expected",
};
//> This is what I expected
```

## Saner string to number comparisons

В пхп 7 такое тсравнение давало истину
```php
0 == 'foobar' // true
```

В пхп 8 для сравнения числа со строкой число преобразуется в строку и сравниваются строки, что дает более точный и ожидаемый результат

```php
0 == 'foobar' // false
```

## Enumerations

```php
enum Status
{
    case Draft;
    case Published;
    case Archived;
}
function acceptStatus(Status $status) {...}
```

Перечисления поддерживают валидацию из коробки

## Readonly Properties

```php
class BlogData
{
    public readonly Status $status;
   
    public function __construct(Status $status) 
    {
        $this->status = $status;
    }
}
```

## Fibers

Файберы представляют собой функции, работа которых может быть прервана и продолжена с передачей параметров между файбером и основным потоком.

Файберы не добавляют в язык асинхронность и настоящую многопоточность, а скорее являеются более удобным вариантом генераторов.

```php
$fiber = new Fiber(function (): void {
    $value = Fiber::suspend('fiber');
    echo "Value used to resume fiber: ", $value, "\n";
});

$value = $fiber->start();
echo "Value from fiber suspending: ", $value, "\n";
$fiber->resume('test');

//Value from fiber suspending: fiber
//Value used to resume fiber: test
```

____________________________________________________
# Base category
- 
____________________________________________________
# Links
- https://www.php.net/releases/8.0/en.php
- https://www.youtube.com/watch?v=u_WEnV_d5OI