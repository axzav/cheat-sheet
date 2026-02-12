# Compare multidimensional arrays

Чтобы сравнить два массива с такой же структурой в PHP, можно воспользоваться рекурсией и функцией `array_diff_assoc()` для сопоставления элементов по ключам и значениям. В случае вложенных массивов следует углубляться в них.

Вот пример функции для рекурсивного сравнения двух массивов:

```php
function compareArrays($array1, $array2) {
    $diff = [];

    foreach ($array1 as $key => $value) {
        if (is_array($value)) {
            // Если это массив, рекурсивно вызываем ту же функцию
            if (!isset($array2[$key]) || !is_array($array2[$key])) {
                $diff[$key] = $value; // Добавляем разницу, если нет ключа или значение не массив
            } else {
                $nestedDiff = compareArrays($value, $array2[$key]);
                if (!empty($nestedDiff)) {
                    $diff[$key] = $nestedDiff;
                }
            }
        } else {
            // Если это не массив, просто сравниваем значения
            if (!array_key_exists($key, $array2) || $array2[$key] !== $value) {
                $diff[$key] = $value;
            }
        }
    }

    return $diff;
}

// Пример использования
$array1 = [
    'm_r' => [
        'admin' => '2109,3773,3825',
        'user' => '46157'
    ],
    'sa_o' => ['00158000002jJKlAAM']
];

$array2 = [
    'm_r' => [
        'admin' => '2109,3773,3825,4353', // добавлен новый элемент
        'user' => '46157'
    ],
    'sa_o' => ['00158000002jJKlAAM']
];

$diff = compareArrays($array1, $array2);
print_r($diff);
```

Эта функция рекурсивно сравнивает значения двух массивов и возвращает массив, содержащий отличия между ними. В данном случае результатом будет массив только с теми значениями, которые отличаются.

Если нужно вернуть разницу как для первого массива, так и для второго, можно выполнить сравнение в обоих направлениях и объединить результат.

```php
$diff1 = compareArrays($array1, $array2);
$diff2 = compareArrays($array2, $array1);

$finalDiff = ['in_array1' => $diff1, 'in_array2' => $diff2];
print_r($finalDiff);
```

Это покажет элементы, которые есть в одном массиве, но отсутствуют в другом.
