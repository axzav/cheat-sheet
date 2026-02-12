# Перечисления (Enums)

Перечисления (enums) появились в PHP 8.1 и позволяют определять набор именованных констант.

## Базовое использование

```php
enum Status
{
    case Draft;
    case Published;
    case Archived;
}

function acceptStatus(Status $status) {
    // ...
}

acceptStatus(Status::Draft);
```

## Backed Enums

Перечисления могут иметь скалярные значения (int или string):

```php
enum Status: string
{
    case Draft = 'draft';
    case Published = 'published';
    case Archived = 'archived';
}

$status = Status::Draft;
echo $status->value; // 'draft'
```

## Методы в перечислениях

Перечисления могут содержать методы:

```php
enum Status: string
{
    case Draft = 'draft';
    case Published = 'published';
    case Archived = 'archived';

    public function label(): string
    {
        return match($this) {
            self::Draft => 'Черновик',
            self::Published => 'Опубликовано',
            self::Archived => 'Архив',
        };
    }
}
```

## Статические методы

```php
enum Status: string
{
    case Draft = 'draft';
    case Published = 'published';

    public static function fromValue(string $value): self
    {
        return match($value) {
            'draft' => self::Draft,
            'published' => self::Published,
            default => throw new ValueError("Invalid status: $value"),
        };
    }
}
```

## Интерфейсы

Перечисления могут реализовывать интерфейсы:

```php
interface Colorful
{
    public function color(): string;
}

enum Suit: string implements Colorful
{
    case Hearts = 'H';
    case Diamonds = 'D';
    case Clubs = 'C';
    case Spades = 'S';

    public function color(): string
    {
        return match($this) {
            self::Hearts, self::Diamonds => 'Red',
            self::Clubs, self::Spades => 'Black',
        };
    }
}
```

## Валидация

Перечисления поддерживают валидацию из коробки — нельзя передать несуществующее значение:

```php
function setStatus(Status $status) {
    // ...
}

setStatus(Status::Draft); // OK
setStatus('draft'); // TypeError
```
