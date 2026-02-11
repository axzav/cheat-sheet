# Работа с датами и временем

PHP предоставляет классы `DateTime`, `DateTimeImmutable` и связанные классы для работы с датами и временем.

## DateTime

### Создание

```php
// Текущая дата и время
$now = new DateTime();

// Конкретная дата
$date = new DateTime('2024-01-15');
$date = new DateTime('2024-01-15 14:30:00');

// Из timestamp
$date = new DateTime('@1617185484');
$utc = $date->getTimestamp();

// С указанием часового пояса
$date = new DateTime('now', new DateTimeZone('Europe/Moscow'));
```

### Форматирование

```php
$date = new DateTime('2024-01-15 14:30:00');
echo $date->format('Y-m-d H:i:s'); // 2024-01-15 14:30:00
echo $date->format('d.m.Y');       // 15.01.2024
```

### Модификация

```php
$date = new DateTime('2024-01-15');
$date->modify('+1 day');
$date->modify('+1 month');
$date->modify('-1 year');

// Или через методы
$date->add(new DateInterval('P1D')); // +1 день
$date->sub(new DateInterval('P1M')); // -1 месяц
```

### Сравнение

```php
$date1 = new DateTime('2024-01-15');
$date2 = new DateTime('2024-01-20');

if ($date1 < $date2) {
    // $date1 раньше $date2
}

$diff = $date1->diff($date2);
echo $diff->days; // Разница в днях
```

## DateTimeImmutable

Неизменяемая версия `DateTime`:

```php
$date = new DateTimeImmutable('2024-01-15');
$newDate = $date->modify('+1 day'); // Возвращает новый объект
// $date остается неизменным
```

## DateInterval

Интервал времени:

```php
// P1D - период 1 день
// P1M - период 1 месяц
// P1Y - период 1 год
// PT1H - период времени 1 час
// PT30M - период времени 30 минут

$interval = new DateInterval('P1Y2M3DT4H5M6S');
$date->add($interval);
```

## DatePeriod

Период для итерации:

```php
$start = new DateTime('2024-01-01');
$end = new DateTime('2024-01-31');
$interval = new DateInterval('P1D');

$period = new DatePeriod($start, $interval, $end);

foreach ($period as $date) {
    echo $date->format('Y-m-d') . "\n";
}
```

## Часовые пояса

```php
// Получить список часовых поясов
$timezones = DateTimeZone::listIdentifiers();

// Создать объект часового пояса
$tz = new DateTimeZone('Europe/Moscow');

// Установить часовой пояс
$date = new DateTime('now', $tz);

// Конвертация между часовыми поясами
$date->setTimezone(new DateTimeZone('UTC'));
```

## Timestamp

```php
// Получить timestamp
$timestamp = time(); // Текущий
$timestamp = $date->getTimestamp(); // Из DateTime

// Создать из timestamp
$date = DateTime::createFromFormat('U', $timestamp);
```

## Полезные функции

```php
// Текущий timestamp
time();

// Форматирование timestamp
date('Y-m-d H:i:s', time());

// Парсинг строки
strtotime('2024-01-15');
strtotime('+1 day');
strtotime('next Monday');

// Проверка валидности даты
checkdate(2, 29, 2024); // true (високосный год)
```
