202301301056
Tags: #
____________________________________________________
# PHP 7.x

## PHPNG (php new generation)

https://wiki.php.net/phpng
http://news.php.net/php.internals/73888

Одним из приоритетов PHP сообщества является улучшение производительности PHP. Начиная с 5.0 было проделано много работы по улучшению ядра виртуальной машины, но с определенного момента дальнейшие улучшения давали лишь пару процентов прироста производительности, что заставило разработчиков обратить внимание на то, что было по-настоящему бутылочным горлышком. Было выявлено, что виртуальной машине требовалось работать со структурами данных, постоянно требующими выделение и освобождение памяти и подсчет ссылок на значения.
PHPNG была экспериментальной веткой PHP, целью которой была попытка оптимизировать выделение памяти. Сложность была в том, что было необходимо провести большой рефакторинг типов данных.

**Рефакторинг внутренних структур данных** и добавление дополнительного этапа перед компиляцией кода в виде абстрактного синтаксического дерева — **Abstract Syntax Tree (AST)**, привели к улучшенной производительности и более эффективному распределению памяти.

В итоге изменения из ветки PHPNG позволили добиться прироста производительности в 10-30% на реальных приложениях и эта ветка стала основой для версии PHP 7.0.

## Нововведения

### Type declarations

Эта функциональность введена в двух опциях: принудительная и строгая.

**Принудительная** обозначает, что при передаче аргумента не того типа будет произведена принудительная конвертация типа к объявленому

**Строгая** обозначает, что при передаче аргумента не того типа будет выброшено исключение TypeError. Строгая проверка типов включается с помощью директивы **declare**

```php
declare(strict_types=1);

function add(int $a, int $b) : int {
  return $a + $b;
}
```

Также была введена декларация типов для полей классов

```php
class User {  
    public int $id;  
    public string $name;  
}
```

### New error handling system

В предыдущих версиях пхп при возникновении некоторых ошибок (**фатальные**) вроде ошибки парсинга, неверных аргументов и тд скрипт полностью останавливался и ошибка показывалась напрямую пользователю.

В пхп 7 систему обработки ошибок переработали. Все ошибки были разделены на 2 основные категории - `Error` и `Exception`
`Error` - бывшие фатальные ошибки, которые теперь так же можно отлавливать
`Exception` - исключения, которые должны быть отловлены и обработаны

```php
try {
  // code that might raise a ParseError
} catch (ParseError | CompileError $e) {
  // code to handle a ParseError
}
```

Также введена возможность отлова нескольких типов исключений в одном блоке catch

### Anonymous classes

```php
interface Logger {
  public function log($message);
}

$logger = new class implements Logger {
  public function log($message) {
    echo $message;
  }
};
```

### Class constant visibility

```php
class ConstDemo  
{  
    const PUBLIC_CONST_A = 1;  
    public const PUBLIC_CONST_B = 2;  
    protected const PROTECTED_CONST = 3;  
    private const PRIVATE_CONST = 4;  
}
```

### Asynchronous signal handling

Введена новая функция `pcntl_async_signals` для отслеживания сигналов вместо использования `ticks`, которые давали большой оверхед

```php
pcntl_async_signals(true); // turn on async signals  
  
pcntl_signal(SIGHUP, function($sig) {  
echo "SIGHUP\n";  
});  
  
posix_kill(posix_getpid(), SIGHUP);
```

### WeakReference

`WeakReference` позволяет создавать ссылку на объект, которая не блокирует удаление объекта сборщиком мусора (не увеличивает `gc_ref_count`)

```php
$object = new stdClass();
$weakRef = WeakReference::create($object);

// Store a reference to the object
$reference = &$object;
unset($object);

// The weak reference still holds the reference to the object, but the object
// is eligible for destruction by the garbage collector because the only
// reference to the object is a weak reference
var_dump($weakRef->valid()); // bool(true)

// The strong reference is unset, so the object can be destroyed by the garbage collector
unset($reference);
var_dump($weakRef->valid()); // bool(false)
```


### Синтаксический сахар

Null coalescing operator
```php
// Fetches the value of $_GET['user'] and returns 'nobody'  
// if it does not exist.  
$username = $_GET['user'] ?? 'nobody';  
// This is equivalent to:  
$username = isset($_GET['user']) ? $_GET['user'] : 'nobody';  
  
// Coalescing can be chained: this will return the first  
// defined value out of $_GET['user'], $_POST['user'], and  
// 'nobody'.  
$username = $_GET['user'] ?? $_POST['user'] ?? 'nobody';
```

Spaceship operator
```php
// Integers  
echo 1 <=> 1; // 0  
echo 1 <=> 2; // -1  
echo 2 <=> 1; // 1  
  
// Floats  
echo 1.5 <=> 1.5; // 0  
echo 1.5 <=> 2.5; // -1  
echo 2.5 <=> 1.5; // 1  
  
// Strings  
echo "a" <=> "a"; // 0  
echo "a" <=> "b"; // -1  
echo "b" <=> "a"; // 1
```

Define arrays
```php
define('ANIMALS', [  
'dog',  
'cat',  
'bird'  
]);  
  
echo ANIMALS[1]; // outputs "cat"
```

Symmetric array destructuring
```php
$data = [  
[1, 'Tom'],  
[2, 'Fred'],  
];  
  
// list() style  
list($id1, $name1) = $data[0];  
  
// [] style  
[$id1, $name1] = $data[0];
```

Support for negative string offsets
```php
var_dump("abcdef"[-2]);  
var_dump(strpos("aabbcc", "b", -3));
```

Arrow functions (короткая форма записи анонимных функций)
```php
$y = 1;
 
$fn1 = fn($x) => $x + $y;
// equivalent to using $y by value:
$fn2 = function ($x) use ($y) {
    return $x + $y;
};

var_export($fn1(3));
```

Null coalescing assignment operator
```php
$array['key'] ??= computeDefault();  
// is roughly equivalent to  
if (!isset($array['key'])) {  
$array['key'] = computeDefault();  
}
```

Unpacking inside arrays
```php
$parts = ['apple', 'pear'];  
$fruits = ['banana', 'orange', ...$parts, 'watermelon'];  
// ['banana', 'orange', 'apple', 'pear', 'watermelon'];
```

____________________________________________________
# Base category
- 
____________________________________________________
# Links
- 