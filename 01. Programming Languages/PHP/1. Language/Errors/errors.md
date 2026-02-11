# Errors

- Типы ошибок
- Настройка протоколирования
- Отображение/логирование

-------

php.ini

error_reporting - уровень протоколирования ошибки
E_ALL & ~E_NOTICE & ~E_STRICT & ~E_DEPRECATED

При указании 0 - не протоколировать никакие ошибки. Даже E_PARSE который останавливает работу скрипта не будет запротоколирован

Использование функции error_reporting не повлияет на E_PARSE, т.к. парсинг произойдет до интерпритации 

```php
// Turn off all error reporting
error_reporting(0);

// Report simple running errors
error_reporting(E_ERROR | E_WARNING | E_PARSE);

// Reporting E_NOTICE can be good too (to report uninitialized
// variables or catch variable name misspellings ...)
error_reporting(E_ERROR | E_WARNING | E_PARSE | E_NOTICE);

// Report all errors except E_NOTICE
error_reporting(E_ALL & ~E_NOTICE);

// Report all PHP errors
error_reporting(E_ALL);

// Report all PHP errors
error_reporting(-1);

// Get current INT value
var_dump(error_reporting());

// Same as error_reporting(E_ALL);
ini_set('error_reporting', E_ALL);
```

------

display_errors - отображать ли ошибки в stdout. On/Off/1/0/stderr. stderr появился в 8.0

Даже если display_errors=Off и error_reporting=0 то при фатальной ошибке все равно будет возвращен 500, т.к. nginx увидит что php-fmp
не отдал заголовки, поймет что скрипт завершился с ошибкой и вернет 500 код

------

