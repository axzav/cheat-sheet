# CLI (Command Line Interface)

PHP может выполняться из командной строки для создания консольных приложений и скриптов.

## Базовое использование

```bash
php script.php
php -r "echo 'Hello World';"
```

## Аргументы командной строки

```php
// $argv содержит аргументы
// $argc содержит количество аргументов

// script.php arg1 arg2
var_dump($argv); // ['script.php', 'arg1', 'arg2']
var_dump($argc); // 3
```

## Чтение из stdin

```php
// Чтение одной строки
$input = trim(fgets(STDIN));

// Чтение до конца
$content = stream_get_contents(STDIN);

// Построчное чтение
while (($line = fgets(STDIN)) !== false) {
    echo $line;
}
```

## Вывод

```php
echo "Output\n";
print "Output\n";
fwrite(STDOUT, "Output\n");
fwrite(STDERR, "Error\n");
```

## Опции командной строки

```bash
php -v              # Версия PHP
php -m              # Список загруженных модулей
php -i              # Информация о конфигурации
php -l script.php   # Проверка синтаксиса
php -S localhost:8000 # Встроенный веб-сервер
php -t /path/to/docroot # Корневая директория для веб-сервера
```

## Встроенный веб-сервер

```bash
# Запуск на порту 8000
php -S localhost:8000

# С указанием корневой директории
php -S localhost:8000 -t public/

# С роутером
php -S localhost:8000 router.php
```

## Обработка сигналов

```php
// Обработка SIGTERM
pcntl_signal(SIGTERM, function($signo) {
    echo "Received SIGTERM\n";
    exit(0);
});

// Обработка SIGINT (Ctrl+C)
pcntl_signal(SIGINT, function($signo) {
    echo "Received SIGINT\n";
    exit(0);
});

// Проверка сигналов в цикле
while (true) {
    pcntl_signal_dispatch();
    // Ваш код
    sleep(1);
}
```

## Интерактивный режим

```bash
php -a  # Интерактивный режим (REPL)
```

## Переменные окружения

```php
// Получить переменную окружения
$path = getenv('PATH');

// Установить переменную окружения
putenv('MY_VAR=value');

// Доступ через $_ENV (если включен)
$var = $_ENV['MY_VAR'] ?? null;
```

## Проверка режима выполнения

```php
if (php_sapi_name() === 'cli') {
    // Код выполняется из CLI
}

if (PHP_SAPI === 'cli') {
    // Альтернативный способ
}
```

## Цветной вывод

```php
// ANSI коды для цветов
echo "\033[31mRed text\033[0m\n";
echo "\033[32mGreen text\033[0m\n";
echo "\033[33mYellow text\033[0m\n";
```

## Прогресс-бар

```php
function showProgress($current, $total) {
    $percent = ($current / $total) * 100;
    $barLength = 50;
    $filled = round(($percent / 100) * $barLength);
    $bar = str_repeat('=', $filled) . str_repeat(' ', $barLength - $filled);
    echo "\r[$bar] $percent%";
}

for ($i = 0; $i <= 100; $i++) {
    showProgress($i, 100);
    usleep(100000);
}
echo "\n";
```
