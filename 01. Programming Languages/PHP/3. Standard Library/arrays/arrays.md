# Массивы

Массивы в PHP — упорядоченные карты (maps), которые связывают значения с ключами.

## Создание массивов

```php
// Короткий синтаксис (PHP 5.4+)
$arr = [1, 2, 3];
$arr = ['key' => 'value'];

// Длинный синтаксис
$arr = array(1, 2, 3);
$arr = array('key' => 'value');
```

## Типы массивов

### Индексированные массивы

```php
$arr = [1, 2, 3];
// Эквивалентно: [0 => 1, 1 => 2, 2 => 3]
```

### Ассоциативные массивы

```php
$arr = ['name' => 'John', 'age' => 30];
```

### Многомерные массивы

```php
$arr = [
    [1, 2, 3],
    [4, 5, 6]
];

$arr = [
    'user' => ['name' => 'John', 'age' => 30]
];
```

## Доступ к элементам

```php
$arr = ['name' => 'John', 'age' => 30];

echo $arr['name'];        // John
echo $arr['age'];         // 30

// Добавление элемента
$arr['email'] = 'john@example.com';

// Изменение элемента
$arr['age'] = 31;
```

## Обработка массивов

### array_map

Применяет callback к каждому элементу массива:

```php
$numbers = [1, 2, 3, 4];
$squared = array_map(fn($n) => $n * $n, $numbers);
// [1, 4, 9, 16]

// С сохранением ключей
$result = array_map(function($code) {
    return (new BackupCode())
        ->setCode($code)
        ->setUsed(false);
}, $this->backupCodes);
```

### array_walk

Применяет callback к каждому элементу массива (изменяет исходный массив):

```php
$arr = ['a' => 1, 'b' => 2];
array_walk($arr, function(&$value, $key) {
    $value *= 2;
});
// $arr теперь ['a' => 2, 'b' => 4]
```

### array_filter

Фильтрует элементы массива:

```php
$numbers = [1, 2, 3, 4, 5];
$even = array_filter($numbers, fn($n) => $n % 2 === 0);
// [1 => 2, 3 => 4] (ключи сохраняются!)
```

### array_reduce

Сводит массив к одному значению:

```php
$numbers = [1, 2, 3, 4];
$sum = array_reduce($numbers, fn($carry, $item) => $carry + $item, 0);
// 10
```

## Работа с ключами

### Проверка существования ключа

```php
$arr = ['name' => 'John'];

isset($arr['name']);        // true
array_key_exists('name', $arr); // true
isset($arr['age']);         // false

// При добавлении можно обращаться к несуществующим ключам
$arr['new_key'] = 'value'; // OK, создаст новый ключ
```

### array_column

Извлекает значения одного столбца из массива массивов:

```php
$data = [
    ['userID' => '76988', 'accountID' => '0'],
    ['userID' => '76989', 'accountID' => '1']
];

$userIDs = array_column($data, 'userID');
// ['76988', '76989']
```

## Деструктуризация (PHP 7.1+)

```php
// Пропуск элементов
[,,,, $userId] = explode('_', $key);

// С ключами
['name' => $name, 'age' => $age] = ['name' => 'John', 'age' => 30];
```

## Полезные функции

```php
count($arr);              // Количество элементов
array_keys($arr);         // Массив ключей
array_values($arr);       // Массив значений
array_merge($arr1, $arr2); // Объединение массивов
array_intersect($arr1, $arr2); // Пересечение
array_diff($arr1, $arr2); // Разница
in_array($value, $arr);   // Проверка наличия значения
array_search($value, $arr); // Поиск ключа по значению
array_unique($arr);       // Уникальные значения
array_reverse($arr);      // Обратный порядок
sort($arr);               // Сортировка
```

## Итерация

```php
// foreach
foreach ($arr as $key => $value) {
    echo "$key: $value\n";
}

// foreach по значению
foreach ($arr as $value) {
    echo "$value\n";
}
```
