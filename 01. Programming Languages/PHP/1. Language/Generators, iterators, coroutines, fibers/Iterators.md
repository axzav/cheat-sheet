# Iterators

## Iterator

`Iterator` в php это интерфейс, который позволяет перебирать данные объекта с помощью оператора `foreach`. Он расширяет интерфейс `Traversable`, который является внутренним интерфейсом и не может быть реализован самостоятельно в коде (только через интерфейс `Iterator` или `IteratorAggregate`)

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

## IteratorAggregate

Реализация этого интерфейса позволяет итерировать объект так же как и интерфейс `Iterator`. Класс, который реализует интерфейс, должен возвращать объект реализующий интерфейс `Traversable` в методе `getIterator()`.

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

## Links

- https://www.php.net/manual/ru/class.iterator.php
- https://www.php.net/manual/ru/language.oop5.iterations.php