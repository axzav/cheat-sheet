# Исключения

Исключения (exceptions) — механизм обработки ошибок, позволяющий отделить обработку ошибок от основного кода.
С PHP 7.0 многие фатальные ошибки заменены на исключения:

```php
try {
    // Код, который может выбросить исключение
} catch (TypeError $e) {
    // Обработка ошибки типа
} catch (Error $e) {
    // Обработка других ошибок
} catch (Exception $e) {
    // Обработка исключений
} finally {
    // Код, выполняемый всегда
}
```

## Базовое использование

```php
try {
    // Код, который может выбросить исключение
    if ($value < 0) {
        throw new InvalidArgumentException('Value must be positive');
    }
} catch (InvalidArgumentException $e) {
    // Обработка конкретного типа исключения
    echo 'Ошибка: ' . $e->getMessage();
} catch (Exception $e) {
    // Обработка всех остальных исключений
    echo 'Общая ошибка: ' . $e->getMessage();
} finally {
    // Код, выполняемый всегда
    echo 'Блок finally выполнен';
}
```

## Создание исключений

```php
class CustomException extends Exception
{
    public function __construct($message = "", $code = 0, Throwable $previous = null)
    {
        parent::__construct($message, $code, $previous);
    }

    public function __toString(): string
    {
        return __CLASS__ . ": [{$this->code}]: {$this->message}\n";
    }
}
```

## Вложенные исключения

```php
try {
    try {
        throw new Exception('Внутреннее исключение');
    } catch (Exception $e) {
        throw new RuntimeException('Внешнее исключение', 0, $e);
    }
} catch (RuntimeException $e) {
    echo $e->getMessage();
    echo $e->getPrevious()->getMessage(); // Внутреннее исключение
}
```

## Методы исключений

```php
$exception = new Exception('Сообщение', 500);

$exception->getMessage();    // Сообщение об ошибке
$exception->getCode();       // Код ошибки
$exception->getFile();       // Файл, где выброшено исключение
$exception->getLine();       // Строка, где выброшено исключение
$exception->getTrace();      // Массив стека вызовов
$exception->getTraceAsString(); // Стек вызовов в виде строки
$exception->getPrevious();   // Предыдущее исключение
```

## Иерархия исключений

```
Throwable
├── Error
│   ├── TypeError
│   ├── ParseError
│   ├── ArithmeticError
│   │   ├── DivisionByZeroError
│   │   └── ...
│   └── ...
└── Exception
    ├── RuntimeException
    ├── LogicException
    │   ├── InvalidArgumentException
    │   ├── DomainException
    │   └── ...
    └── ...
```


## Лучшие практики

1. Используйте конкретные типы исключений
2. Предоставляйте информативные сообщения
3. Не используйте исключения для контроля потока выполнения
4. Логируйте исключения перед их обработкой
5. Используйте `finally` для очистки ресурсов
