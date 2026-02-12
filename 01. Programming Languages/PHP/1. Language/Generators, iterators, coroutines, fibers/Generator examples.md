# Generator examples

Генераторы обычно удобно использовать при работе с большими файлами или генерацией бесконечных значений без хранения их всех в памяти.

## Reading large file

С помощью генератора можно читать очень большой файл без выгрузки его в память:

```php
function readLines($filePath) {
    $file = fopen($filePath, 'r');
    if ($file) {
        while (($line = fgets($file)) !== false) {
            yield $line;
        }
        fclose($file);
    }
}

// Usage example
$filePath = 'large_file.txt';
foreach (readLines($filePath) as $line) {
    // Process each line of the file
}
```

## Generation of an infinite sequence

Генераторы позволяют генерировать бесконечный набор данных без необходимости хранить данные в памяти:

```php
function fibonacci() {
    $a = 0;
    $b = 1;
    while (true) {
        yield $a;
        $temp = $a;
        $a = $b;
        $b = $temp + $b;
    }
}

$generator = fibonacci();
foreach ($generator as $i => $number) {
    if ($i >= 10) {
        break;
    }
    echo $number . PHP_EOL;
}
```

## Streaming data from an API

С помощью генераторов можно получать данные из АПИ в поточном стиле:

```php
function fetchApiData($apiUrl, $itemsPerPage) {
    $currentPage = 1;
    while (true) {
        $url = $apiUrl . "?page={$currentPage}&per_page={$itemsPerPage}";
        $data = json_decode(file_get_contents($url), true);
        if (empty($data)) {
            break;
        }
        foreach ($data as $item) {
            yield $item;
        }
        $currentPage++;
    }
}

// Usage example
$apiUrl = 'https://api.example.com/data';
$itemsPerPage = 50;
foreach (fetchApiData($apiUrl, $itemsPerPage) as $item) {
    // Process each item from the API
}
```

## Loading large DB dataset

С помощью курсоров БД и генераторов можно читать большой объем данных из БД без выгрузки всего датасета в память

```php
$dsn = "mysql:host=localhost;dbname=your_database";
$username = "your_username";
$password = "your_password";

try {
    $pdo = new PDO($dsn, $username, $password);
    $pdo->setAttribute(PDO::ATTR_CURSOR, PDO::CURSOR_SCROLL);
} catch (PDOException $e) {
    die("Error connecting to the database: " . $e->getMessage());
}
```

```php
function fetchRows(PDO $pdo, $query, $chunkSize = 100) {
    $stmt = $pdo->prepare($query, [PDO::ATTR_CURSOR => PDO::CURSOR_SCROLL]);
    $stmt->execute();

    while (true) {
        $rows = [];
        for ($i = 0; $i < $chunkSize; $i++) {
            $row = $stmt->fetch(PDO::FETCH_ASSOC, PDO::FETCH_ORI_NEXT);
            if ($row === false) {
                break;
            }
            $rows[] = $row;
        }

        if (empty($rows)) {
            break;
        }

        foreach ($rows as $row) {
            yield $row;
        }
    }
}
```

```php
$query = "SELECT * FROM large_table";
$chunkSize = 100;

foreach (fetchRows($pdo, $query, $chunkSize) as $row) {
    // Process each row from the database
}
```
