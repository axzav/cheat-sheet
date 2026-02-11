# readfile

Функция `readfile()` читает файл и отправляет его содержимое в выходной буфер.

## Базовое использование

```php
readfile('file.txt');
```

## Отправка файла для скачивания

```php
$file = 'path/to/file.zip';

if (file_exists($file)) {
    // Установка заголовков
    header('Content-Description: File Transfer');
    header('Content-Type: application/octet-stream');
    header('Content-Disposition: attachment; filename="' . basename($file) . '"');
    header('Expires: 0');
    header('Cache-Control: must-revalidate');
    header('Pragma: public');
    header('Content-Length: ' . filesize($file));
    
    // Чтение и отправка файла
    readfile($file);
    exit;
} else {
    http_response_code(404);
    echo "File not found.";
}
```

## Отправка файла для просмотра

```php
$file = 'path/to/image.jpg';

if (file_exists($file)) {
    header('Content-Type: ' . mime_content_type($file));
    header('Content-Length: ' . filesize($file));
    readfile($file);
    exit;
}
```

## Заголовки HTTP

- `Content-Description` - описание содержимого
- `Content-Type` - MIME тип файла
- `Content-Disposition` - как обрабатывать файл (inline для просмотра, attachment для скачивания)
- `Content-Length` - размер файла в байтах
- `Expires` - время истечения кэша
- `Cache-Control` - управление кэшем
- `Pragma` - устаревший заголовок для кэша

## Безопасность

Всегда проверяйте:
- Существование файла
- Права доступа
- Путь файла (защита от directory traversal)

```php
$file = 'path/to/file.txt';
$realPath = realpath($file);

// Проверка, что файл находится в разрешенной директории
$allowedDir = '/var/www/uploads/';
if (strpos($realPath, $allowedDir) !== 0) {
    die('Access denied');
}

if (file_exists($realPath)) {
    header('Content-Type: ' . mime_content_type($realPath));
    readfile($realPath);
}
```
