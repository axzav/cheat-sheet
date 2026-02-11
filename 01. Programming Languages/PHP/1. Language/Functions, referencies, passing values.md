202304152143
Tags: #
____________________________________________________
# Functions, referencies, passing values

## References

При указании ссылки на переменную, обе переменные будут указывать на одую и ту же область в памяти. При изменении значения одной переменной, другая перменная которая ссылается на ту же переменную так же будет изменена:

```php
$a = 10;
$b = &$a; // $b is a reference to $a

$b++;
echo $a; // Output: 11 (changed)
```

## Passing by reference

При передаче перменной скалярного типа или массива по ссылке, изменение переменной в теле функции также изменит ее значение в родильском скоупе:

```php
function addOne(&$number) {
    $number++;
}

$value = 5;
addOne($value);
echo $value; // Output: 6 (changed)
```

## Copy on write

Механизм `copy-on-write` используется для оптимизации памяти при присвоении перменных или передаче аргумента функции. Когда происходит передача по значению, значение не будет скопировано сразу же и обе переменные будут указывать на одну и ту же область в памяти. Копирование произойдет в момент того, как переменная будет изменена:

```php
$a = 'some large string';
$b = $a; // Both $a and $b point to the same memory location

$b[0] = 'S'; // Now, PHP creates a new copy for $b, and $a remains unchanged
```


## Passing values

Разные типы могут быть переданы в функцию или по значению или по ссылке.

**Скалярные типы**
Скалярные типы (integers, floats, strings, and booleans) всегда передаются по значению, т.е. значение будет скоприровано в функцию. Механизм `copy-on-write` будет использован для оптимизации:

```php
function modifyScalar($value) {
    $value = 20;
}

$number = 10;
modifyScalar($number);
echo $number; // Output: 10 (unchanged)
```

**Массивы**
Так же как и скалярные типы - значение будет скопировано (механизм `copy-on-write`) также будет задействован:

```php
function modifyArray($array) {
    $array[0] = 20;
}

$numbers = [10, 15];
modifyArray($numbers);
print_r($numbers); // Output: Array ( [0] => 10 [1] => 15 ) (unchanged)
```

Если передается массив объектов, то при изменении элемента массива объекты в каждом элементе не будут скопированы, т.к. каждый элемент - ссылка на объект.

**Объекты**
Объекты всегда передаются по ссылке. Это значит, что если объект был изменен в функции, то это затронет также и переменную которая ссылается на этот объект вне скоупа функции:

```php
class MyClass {
    public $value;

    public function __construct($value) {
        $this->value = $value;
    }
}

function modifyObject($object) {
    $object->value = 20;
}

$myObject = new MyClass(10);
modifyObject($myObject);
echo $myObject->value; // Output: 20 (changed)
```

**Ресурсы**
При передаче ресурса в функцию, переменная будет скопирована, однако внутри эта переменная все равно будет ссылаться на тот же ресурс:

```php
function modifyFile($file) {
    fwrite($file, "Hello, World!");
}

$file = fopen('example.txt', 'w');
modifyFile($file);
fclose($file);
```

____________________________________________________
# Base category
- 
____________________________________________________
# Links
- 