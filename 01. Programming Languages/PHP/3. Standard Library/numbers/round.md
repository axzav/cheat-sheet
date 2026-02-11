# Float round

```php
round(float $num, int $precision = 0, int $mode = PHP_ROUND_HALF_UP): float
```
Round позволяет округлять до определенной точности
```php
round(3.14159);             // float(3)
round(3.14159, 2);          // float(3.14)
round(2.5);                 // float(3)  (по умолчанию: HALF_UP)
round(1.95583, 2);          // 1.96

// PHP_ROUND_HALF_UP    округление вверх при .5 (по умолчанию)
// PHP_ROUND_HALF_DOWN  округление вниз при .5
// PHP_ROUND_HALF_EVEN  округление к ближайшему чётному
// PHP_ROUND_HALF_ODD   округление к ближайшему нечётному

round(2.5, 0, PHP_ROUND_HALF_DOWN); // float(2)
round(2.5, 0, PHP_ROUND_HALF_EVEN); // float(2)
round(2.5, 0, PHP_ROUND_HALF_ODD);  // float(3)
```

`ceil` округляет всегда **вверх** до ближайшего целого числа.
```php
ceil(3.1);   // float(4)
ceil(-3.1);  // float(-3)
```

`floor` округляет всегда **вниз** до ближайшего целого.
```php
floor(3.9);   // float(3)
floor(-3.1);  // float(-4)
```

`intval()` или `(int)` Приводит число к целому путём усечения дробной части (не округления).
```php
(int) 3.9;   // int(3)
(int) -3.9;  // int(-3)
```