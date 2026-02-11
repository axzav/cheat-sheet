# Объединенные и пересекающиеся типы

## Объединенные типы (Union Types) - PHP 8.0+

Объединенные типы позволяют указать, что параметр или возвращаемое значение может быть одного из нескольких типов.

```php
function processValue(int|string $value): int|string {
    if (is_int($value)) {
        return $value * 2;
    }
    return strtoupper($value);
}

processValue(42);    // 84
processValue('hello'); // 'HELLO'
```

### Nullable типы

В PHP 8.0+ `?Type` эквивалентно `Type|null`:

```php
function processValue(?string $value): ?string {
    return $value ? strtoupper($value) : null;
}

// Эквивалентно:
function processValue(string|null $value): string|null {
    return $value ? strtoupper($value) : null;
}
```

### Использование в свойствах

```php
class User {
    public int|string $id; // ID может быть int или string
    public string|null $email; // Email может быть строкой или null
}
```

## Пересекающиеся типы (Intersection Types) - PHP 8.1+

Пересекающиеся типы позволяют указать, что значение должно соответствовать всем указанным типам одновременно (обычно интерфейсам).

```php
interface Countable {
    public function count(): int;
}

interface Iterator {
    public function current(): mixed;
    public function next(): void;
}

function processCollection(Countable&Iterator $collection): void {
    // $collection должен реализовывать и Countable, и Iterator
    echo "Count: " . $collection->count() . "\n";
    echo "Current: " . $collection->current() . "\n";
}
```

### Реальный пример

```php
use Symfony\Component\Serializer\Normalizer\DenormalizerInterface;
use Symfony\Component\Serializer\Normalizer\NormalizerInterface;
use Symfony\Component\Serializer\SerializerInterface;

class DataFormatter
{
    public function __construct(
        private (NormalizerInterface&DenormalizerInterface)|SerializerInterface $transformer,
    ) {
        // $transformer должен быть либо объектом, реализующим 
        // и NormalizerInterface, и DenormalizerInterface,
        // либо объектом, реализующим SerializerInterface
    }
}
```

## Комбинация union и intersection

```php
function process(
    (Countable&Iterator)|array $data
): void {
    // $data может быть либо объектом, реализующим Countable и Iterator,
    // либо массивом
}
```

## Ограничения

- Пересекающиеся типы могут использоваться только с интерфейсами и трейтами
- Нельзя использовать пересекающиеся типы с классами
- В объединенных типах нельзя смешивать `false` и `bool` (используйте `bool|false` явно)

## Использование в типах возвращаемых значений

```php
function findUser(int $id): User|null {
    // ...
}

function getValue(): string|int|false {
    // Возвращает string, int или false
}
```
