202302101947
Tags: #
____________________________________________________
# Generators, iterators, corutines, fibers

## Iterator

`Iterator` в php это интерфейс который позволяет перебирать данные объекта с помощью оператора `foreach`. Он расширяет интерфейс `Traversable`, который является внутренним интерфейсом и не может быть реализован самостоятельно в коде (только через интерфес `Iterator` или `IteratorAggregate`)

```php
interface Iterator extends Traversable
{
    public current(): mixed
    public key(): mixed
    public next(): void
    public rewind(): void
    public valid(): bool
}
```

Можно создать класс, через который можно итерировать `csv` файл

```php

//Тут ошибка - надо поправить и разобраться с интерфейсом Iterator

CsvIterator implements \Iterator
{
    const ROW_SIZE = 4096;

    protected $filePointer = null;
    protected $currentElement = null;
    protected $rowCounter = null;
    protected $delimiter = null;

    public function __construct($file, $delimiter = ',')
    {
        try {
            $this->filePointer = fopen($file, 'rb');
            $this->delimiter = $delimiter;
        } catch (\Exception $e) {
            throw new \Exception('The file "' . $file . '" cannot be read.');
        }
    }

    /**
     * This method resets the file pointer.
     */
    public function rewind(): void
    {
        $this->rowCounter = 0;
        rewind($this->filePointer);
    }

    /**
     * This method returns the current CSV row as a 2-dimensional array.
     *
     * @return array The current CSV row as a 2-dimensional array.
     */
    public function current(): array
    {
        $this->currentElement = fgetcsv($this->filePointer, self::ROW_SIZE, $this->delimiter);
        $this->rowCounter++;

        return $this->currentElement;
    }

    /**
     * This method returns the current row number.
     *
     * @return int The current row number.
     */
    public function key(): int
    {
        return $this->rowCounter;
    }

    /**
     * This method checks if the end of file has been reached.
     *
     * @return bool Returns true on EOF reached, false otherwise.
     */
    public function next(): bool
    {
        if (is_resource($this->filePointer)) {
            return !feof($this->filePointer);
        }

        return false;
    }

    /**
     * This method checks if the next row is a valid row.
     *
     * @return bool If the next row is a valid row.
     */
    public function valid(): bool
    {
        if (!$this->next()) {
            if (is_resource($this->filePointer)) {
                fclose($this->filePointer);
            }

            return false;
        }

        return true;
    }
}

/**
 * The client code.
 */
$csv = new CsvIterator(__DIR__ . '/cats.csv');

foreach ($csv as $key => $row) {
    print_r($row);
}

```

1. Before the first iteration of the loop, Iterator::rewind() is called.
2. Before each iteration of the loop, Iterator::valid() is called.
3a. It Iterator::valid() returns false, the loop is terminated.
3b. If Iterator::valid() returns true, Iterator::current() and
Iterator::key() are called.
4. The loop body is evaluated.
5. After each iteration of the loop, Iterator::next() is called and we repeat from step 2 above.

This is roughly equivalent to:

```php
$it->rewind();

while ($it->valid())
{
    $key = $it->key();
    $value = $it->current();

    // ...

    $it->next();
}
```

[[Iterator use cases]]

## IteratorAggregate

Реализация этого интерфеса позволяет итерировать объект так же как и интерфес `Iterator`. Класс который реализует интерфес должен возвращать объект реализующий интерфес `Traversable` в методе `getIterator()`.

Интерфейс:
```php
interface IteratorAggregate extends Traversable {
    public getIterator(): Traversable
}
```

Пример использования:
```php
class MyGeneratorCollection implements IteratorAggregate {
    private $items;

    public function __construct($items) {
        $this->items = $items;
    }

    public function getIterator() {
        // Use a generator to yield items one by one
        foreach ($this->items as $item) {
            yield $item;
        }
    }
}

$myItems = [1, 2, 3, 4, 5];
$myGeneratorCollection = new MyGeneratorCollection($myItems);

foreach ($myGeneratorCollection as $item) {
    echo $item . PHP_EOL;
}
```

[[Iterator use cases]]

## Generator

Генератор, в общем смысле, функция которую можно прервать и возобновить позже. При этом она может возвращать значения после прерывания.

Генераторы позволяют создавать функции итераторы проще, чем используя интерфейс `Iterator`. При этом итерировать генератор можно только вперед. Функция генератор при вызове возвращает объект `Generator` , который реализует интерфейс `Iterator`. Никакой код перед `yield` не будет выполнен до момента вызова `Generator::current()`.

```php
$gen = (function() {
    yield 1;
    yield 2;

    return 3;
})();

foreach ($gen as $val) {
    echo $val, PHP_EOL;
}

echo $gen->getReturn(), PHP_EOL;

// 1
// 2
// 3
```

Генератор также может содержать `return`. Он не будет учитываться в итерации, но его можно получить отдельно после итерации.

Генератор может возвращать ключ - значение -> `yield 'key' => 'value'`

## Corutines

Генераторы позволяют отправлять данные из генератора в родительскую рутину и получать их из родительской рутины.

Корутины могут управлять тем, где продолжат свое выполнение после `yield`, но генератор может передавать управление только обратно родительской рутине откуда он был вызван. Такие корутины называются **semi-corutines**.

С помощью генераторов в пхп можно реализовать **совместную многозадачность** - несколько сопрограмм могут передавать управление друг другу, каждая сопрограмма решает сама, когда она передает управление другой сопрограмме. Таким образом, можно без использования многопоточности выполнять несколько независиммых задач в одном потоке выделяя на каждую задачу какое-то время и затем переключиться на другую задачу.

![](../../../../attachment/bdf0419dd8dfa010586f0f31eb87fd79.png)

В пхп для реализации совместной многозадачности нужно создать собственый обработчик задач - https://habr.com/ru/post/164173/

Механизм обеспечивается с помошью метода `send` генератора, который позволяет отправлять данные в генератор и получать их из генератора:

```php

function gen() {
    $ret = (yield 'yield1');
    var_dump($ret);
    $ret = (yield 'yield2');
    var_dump($ret);
}

$gen = gen();
var_dump($gen->current());    // string(6) "yield1"
var_dump($gen->send('ret1')); // string(4) "ret1"   (the first var_dump in gen)
                              // string(6) "yield2" (the var_dump of the ->send() return value)
var_dump($gen->send('ret2')); // string(4) "ret2"   (again from within gen)
                              // NULL               (the return value of ->send())

```

[[Generator use cases]]

## Fiber

Файбер - волокно. Тред - нить. Нить состоит из волокон.

Файбер это специализированный класс для создания корутин, который дает более удобный синтаксис и содержит собственный стек вызовов (https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/)

https://wiki.php.net/rfc/fibers

```php
$fiber = new Fiber(function (): void {
  $value = Fiber::suspend('fiber');
  echo "Value used to resume fiber: ", $value, "\n";
});

$value = $fiber->start();

echo "Value from fiber suspending: ", $value, "\n";

$fiber->resume('test');
```

```bash
Value from fiber suspending: fiber
Value used to resume fiber: test
```

____________________________________________________
# Base category
- 
____________________________________________________
# Links
- https://habr.com/ru/post/164173/ - реализация корутин на пхп
- https://www.slideshare.net/chtalbert/from-generator-to-fiber-the-road-to-coroutine-in-php - слайд по корутинам, генераторам и файберам
- https://www.php.net/manual/ru/class.iterator.php
- https://www.php.net/manual/ru/language.generators.comparison.php
- https://www.php.net/manual/ru/language.oop5.iterations.php
