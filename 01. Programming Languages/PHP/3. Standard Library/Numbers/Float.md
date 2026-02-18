# Floating point numbers

Тип float (или double, синоним) используется для хранения **ПРИБЛИЗИТЕЛЬНЫХ** значений с дробной частью.
**Деньги** никогда не хранятся во float. Либо переводить в центы/копейки, либо использовать BCMath или GMP

```php
$a = 1.234;
$b = 1.2e3;
$c = 7E-10;
$d = 1_234.567; // Начиная с PHP 7.4.0
```

При **переполнении** +/-INF
```php
$a = 1.8e308;
var_dump($a); // float(INF)

$a = -1.8e308;
var_dump($a); // float(-INF)

echo INF + 1000;    // INF
echo INF - INF;     // NAN (Not a Number)
```

**Точность** до ~14 значащих цифр
```php
$a = (float) 1234567890123;     // ✅ float(1234567890123)
$a = (float) 12345678901234567; // ❌ float(12345678901234568)
```

## Валидация
```php
filter_var("123.45", FILTER_VALIDATE_FLOAT);    // 123.45 ✅
filter_var("   123.45", FILTER_VALIDATE_FLOAT); // 123.45 ✅
filter_var("123abc", FILTER_VALIDATE_FLOAT);    // false ❌
filter_var("abc123", FILTER_VALIDATE_FLOAT);    // false ❌
filter_var("1.2e3", FILTER_VALIDATE_FLOAT);     // 1200 ✅
filter_var("3,14", FILTER_VALIDATE_FLOAT);      // false ❌ (запятая)
filter_var("", FILTER_VALIDATE_FLOAT);          // false ❌
```

## Погрешность округления при операциях
Не каждая десятичная дробь может быть представлена в двоичном виде
```php
printf("%.55f\n", 0.1); // 0.1000000000000000055511151231257827021181583404541016
var_dump(0.1); //0.1 (var_dump обрезает значение до ~14-17 значащих цифр)
```

При простых операциях могут возникать погрешности и накапливаться ошибки
```php
var_dump(0.1 + 0.2);     // float(0.30000000000000004)
floor((0.1 + 0.7) * 10)  // 7 вместо 8
```

Из этого следует, что никогда не нужно сравнивать float напрямую, нужно использовать допуски
```php
function floats_are_equal($a, $b, $epsilon = 0.00001) {
    return abs($a - $b) < $epsilon;
}

var_dump(floats_are_equal(0.1 + 0.2, 0.3)); // true
```

Если требуется ограниченная точность сравнения до известного небольшого количества значащих цифр, то можно использовать round:
```php
$a = 0.1 + 0.2 + 0.3; //0.6000000000000001
$b = 0.6;
var_dump(round($a, 2) === round($b, 2)); // true
```

## Преобразование из строки
```php
var_dump((float)"123.45");         // float(123.45)
var_dump((float)"   123.45abc");   // float(123.45)
var_dump((float)"-10.5e2");        // float(-1050)
var_dump((float)"0.00123text");    // float(0.00123)
var_dump((float)"abc123");         // float(0) ❗
var_dump((float)"3,14");           // float(3) ❗️ Локализация, НЕ работает в русском формате
```

## Предопределённые константы

**PHP_FLOAT_MAX**
float(1.7976931348623157E+308)

**PHP_FLOAT_MIN**
float(2.2250738585072014E-308)
