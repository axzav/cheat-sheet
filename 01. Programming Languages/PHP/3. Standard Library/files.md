# Работа с файлами

## Чтение файлов

### file_get_contents

```php
// Чтение всего файла
$content = file_get_contents('file.txt');

// С контекстом
$context = stream_context_create([
    'http' => [
        'method' => 'GET',
        'header' => 'User-Agent: MyApp'
    ]
]);
$content = file_get_contents('http://example.com', false, $context);
```

### fopen/fread/fclose

```php
$handle = fopen('file.txt', 'r');
if ($handle) {
    $content = fread($handle, filesize('file.txt'));
    fclose($handle);
}
```

### Построчное чтение

```php
$handle = fopen('file.txt', 'r');
if ($handle) {
    while (($line = fgets($handle)) !== false) {
        echo $line;
    }
    fclose($handle);
}
```

### file()

```php
// Чтение файла в массив строк
$lines = file('file.txt', FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
```

## Запись в файлы

### file_put_contents

```php
// Запись
file_put_contents('file.txt', $content);

// Добавление в конец
file_put_contents('file.txt', $content, FILE_APPEND);

// Блокировка при записи
file_put_contents('file.txt', $content, LOCK_EX);
```

### fopen/fwrite/fclose

```php
$handle = fopen('file.txt', 'w');
if ($handle) {
    fwrite($handle, $content);
    fclose($handle);
}
```

## Проверка файлов

```php
file_exists('file.txt');    // Существует ли файл
is_file('file.txt');        // Является ли файлом (не директорией)
is_readable('file.txt');    // Можно ли читать
is_writable('file.txt');    // Можно ли писать
is_executable('file.txt');  // Можно ли выполнять
```

## Информация о файле

```php
filesize('file.txt');       // Размер в байтах
filemtime('file.txt');      // Время последней модификации
filectime('file.txt');      // Время создания
fileatime('file.txt');      // Время последнего доступа
filetype('file.txt');       // Тип (file, dir, link)
mime_content_type('file.txt'); // MIME тип
```

## Работа с директориями

```php
mkdir('directory');         // Создать директорию
rmdir('directory');        // Удалить директорию
is_dir('directory');       // Является ли директорией
opendir('directory');      // Открыть директорию
readdir($handle);          // Читать директорию
closedir($handle);         // Закрыть директорию
scandir('directory');      // Список файлов в директории
```

## Удаление и переименование

```php
unlink('file.txt');         // Удалить файл
rename('old.txt', 'new.txt'); // Переименовать/переместить
copy('source.txt', 'dest.txt'); // Копировать
```

## Временные файлы

```php
$temp = tmpfile();          // Создать временный файл
$tempName = tempnam('/tmp', 'prefix'); // Имя временного файла
```

## Блокировка файлов

```php
$handle = fopen('file.txt', 'r+');
if (flock($handle, LOCK_EX)) {
    // Эксклюзивная блокировка
    fwrite($handle, $content);
    flock($handle, LOCK_UN); // Снять блокировку
}
fclose($handle);
```

## Потоки (streams)

```php
// php://input - входные данные
$input = file_get_contents('php://input');

// php://output - выходные данные
file_put_contents('php://output', $content);

// php://memory - память
$handle = fopen('php://memory', 'r+');
fwrite($handle, $content);
rewind($handle);
$content = fread($handle, 1024);
```
