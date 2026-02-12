# Анонимные функции и замыкания

Анонимные функции (лямбда-функции) — функции без имени, которые можно создавать на лету. Особенно полезны для callbacks, сортировки, фильтрации коллекций.
Для создания анонимных функций используется класс `Closure`.

## Основные особенности

- Присваивание переменной: обычно присваиваются переменным и вызываются через них
- Замыкания (**closures**): могут использовать переменные из родительской области видимости через `use`
- Параметры и возвращаемые значения: как и обычные функции

## Синтаксис

```php
// Простая анонимная функция
$greet = function($name) {
    return "Привет, $name!";
};

echo $greet("Алиса");  // Привет, Алиса!

// Использование переменных из внешней области видимости
$prefix = 'Привет, ';
$greet = function($name) use ($prefix) {
    return $prefix . $name . '!';
};

echo $greet("Боб");  // Привет, Боб!
```

## Захват переменных через use

По умолчанию переменные из `use` копируются по значению:

```php
$var = 1;

$closure = function() use ($var) {  
    echo $var;  
};  

$var = 2;
$closure(); // 1
```

Для передачи по ссылке используется `&`:

```php
$var = 1;

$closure = function() use (&$var) {
    echo $var;
};

$var = 2;
$closure(); // 2
```

## Рекурсивные вызовы

Для рекурсивного вызова нужно передать ссылку на саму функцию:

```php
$factorial = function($n) use (&$factorial) {
    if ($n == 1) return 1;
    return $factorial($n - 1) * $n;
};

echo $factorial(5); // 120
```

## Контекст $this

Внутри анонимной функции по умолчанию доступен объект `$this`, если функция находится внутри класса:

```php
class Foo
{
    private function bar(): void
    {
        echo 'private bar';
    }

    public function biz(): void
    {
        $closure = function() {
            $this->bar();
        };
        $closure();
    }
}

$foo = new Foo();
$foo->biz(); // private bar
```

Для предотвращения связывания с `$this` используется `static`:

```php
$closure = static function() {
    // $this недоступен
};
```

## Стрелочные функции (PHP 7.4+)

Короткая запись анонимных функций с автоматическим захватом переменных по значению:

```php
$y = 1;

$fn1 = fn($x) => $x + $y;
// Эквивалентно:
$fn2 = function ($x) use ($y) {
    return $x + $y;
};
```

## Callable типы

Класс `Closure` связан с псевдотипом `callable`. К `callable` также относятся:

- Строки с именем функции: `'functionName'`
- Квалифицированные имена методов: `'Foo::bar'` или `[Foo::class, 'bar']`
- Объекты с методом `__invoke()`

## Анонимные классы (PHP 7.0+)

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

## Применение

- Обработка коллекций: `array_map()`, `array_filter()`, `array_reduce()`
- Callbacks для обработчиков событий
- Определение маршрутов в веб-фреймворках
- Создание компактного кода без необходимости именованных функций

## Links

- https://www.php.net/functions.anonymous
- https://wiki.php.net/rfc/closures
- https://www.php.net/manual/ru/language.oop5.anonymous.php
