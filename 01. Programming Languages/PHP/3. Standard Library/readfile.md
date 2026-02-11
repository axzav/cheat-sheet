В PHP можно отдать файл в браузер, используя заголовки HTTP, чтобы указать, что нужно скачать файл, и функцию `readfile` для его чтения. Вот пример кода, который демонстрирует, как это сделать:

```php
<?php
// Путь к файлу, который нужно отдать
$file = 'path/to/your/file.zip';

// Проверяем, существует ли файл
if (file_exists($file)) {
    // Устанавливаем заголовки для скачивания файла
    header('Content-Description: File Transfer');
    header('Content-Type: application/octet-stream');
    header('Content-Disposition: attachment; filename="'.basename($file).'"');
    header('Expires: 0');
    header('Cache-Control: must-revalidate');
    header('Pragma: public');
    header('Content-Length: ' . filesize($file));
    
    // Читаем файл и отправляем его содержимое в выходной буфер
    readfile($file);
    exit;
} else {
    // Обрабатываем ошибку, если файл не найден
    echo "File not found.";
}
?>
```

### Пояснение кода:

1. **Определение пути к файлу**:

   ```php
   $file = 'path/to/your/file.zip';
   ```

2. **Проверка существования файла**:

   ```php
   if (file_exists($file)) {
   ```

3. **Установка заголовков HTTP**:

    * `Content-Description: File Transfer` — описывает передаваемое содержимое.
    * `Content-Type: application/octet-stream` — тип содержимого (общий тип для бинарных данных).
    * `Content-Disposition: attachment; filename="..."` — заголовок, указывающий, что содержимое должно быть загружено и сохранено с указанным именем файла.
    * `Expires: 0` — заголовок указывает, что содержимое сразу устаревает.
    * `Cache-Control: must-revalidate` и `Pragma: public` — заголовки для кэширования.
    * `Content-Length: ...` — размер содержимого.

4. **Отправка содержимого файла в браузер**:

   ```php
   readfile($file);
   exit;
   ```

5. **Обработка ошибки**:
   Если файл не найден, выводится сообщение об ошибке.

Этот пример демонстрирует базовый способ отправки файла в браузер. В реальных приложениях может потребоваться дополнительная обработка ошибок и безопасность, чтобы избежать несанкционированного доступа к файлам.
