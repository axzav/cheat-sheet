202304152000
Tags: #
____________________________________________________
# Iterator use cases

Итераторы, так же как и генераторы, можно использовать для чтения больших файлов и генерации бесконечного потока данных, однако использовать генераторы для этих целей удобнее.

Итераторы удобно использовать для пагинации, обхода сложных структур и фильтрации данных.

## Pagination

```php
class PaginatorIterator implements Iterator {
    // Custom logic to fetch a specific range of records from the data source
}

// Usage example
$itemsPerPage = 10;
$currentPage = 2;
$paginator = new PaginatorIterator($dataSource, $itemsPerPage, $currentPage);

foreach ($paginator as $item) {
    // Display the item on the web page
}
```

## Composite data structures

Если необходимо перебрать в цикле какую-то сложную структуру, то можно обернуть логику обхода в класс итератора:

```php
class TreeIterator implements Iterator {
    // Custom logic to traverse a tree structure
}

// Usage example
$tree = new Tree(...);
$treeIterator = new TreeIterator($tree);

foreach ($treeIterator as $node) {
    // Process tree nodes
}
```

## Filtering data

Существует спецальный интерфейс `FilterIterator`, с помощью которого можно фильтровать данные по определенным критериям:

```php
class FilterIterator extends FilterIterator {
    protected $filter;

    public function __construct(Iterator $iterator, callable $filter) {
        parent::__construct($iterator);
        $this->filter = $filter;
    }

    public function accept() {
        return call_user_func($this->filter, $this->current());
    }
}

// Usage example
$items = new ArrayIterator([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);
$evenItems = new FilterIterator($items, function ($item) {
    return $item % 2 === 0;
});

foreach ($evenItems as $evenItem) {
    // Process even items
}
```

____________________________________________________
# Base category
- 
____________________________________________________
# Links
- 