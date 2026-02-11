# Сетевые функции

## HTTP запросы

### file_get_contents

```php
// Простой GET запрос
$content = file_get_contents('http://example.com/api');

// С контекстом
$context = stream_context_create([
    'http' => [
        'method' => 'POST',
        'header' => [
            'Content-Type: application/json',
            'Authorization: Bearer token'
        ],
        'content' => json_encode(['key' => 'value'])
    ]
]);
$content = file_get_contents('http://example.com/api', false, $context);
```

### cURL

```php
$ch = curl_init('http://example.com/api');

// Настройки
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode(['key' => 'value']));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Content-Type: application/json',
    'Authorization: Bearer token'
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
$error = curl_error($ch);

curl_close($ch);
```

## Работа с URL

```php
// Парсинг URL
$url = 'http://user:pass@example.com:8080/path?query=value#fragment';
$parsed = parse_url($url);
// ['scheme' => 'http', 'host' => 'example.com', ...]

// Построение URL
$url = http_build_query(['key' => 'value', 'foo' => 'bar']);
// key=value&foo=bar

// Кодирование/декодирование
urlencode($str);    // Кодирование для URL
urldecode($str);    // Декодирование из URL
rawurlencode($str); // Raw кодирование
rawurldecode($str); // Raw декодирование
```

## DNS

```php
gethostbyname('example.com');     // IP адрес
gethostbyaddr('93.184.216.34');   // Имя хоста
gethostbynamel('example.com');     // Массив IP адресов
checkdnsrr('example.com', 'MX');  // Проверка DNS записи
```

## Сокеты

```php
// TCP сокет
$socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
socket_connect($socket, 'example.com', 80);

$message = "GET / HTTP/1.1\r\nHost: example.com\r\n\r\n";
socket_write($socket, $message, strlen($message));

$response = socket_read($socket, 1024);
socket_close($socket);
```

## Проверка соединения

```php
// Проверка доступности хоста
$host = 'example.com';
$port = 80;
$connection = @fsockopen($host, $port, $errno, $errstr, 5);

if ($connection) {
    echo "Host is reachable";
    fclose($connection);
} else {
    echo "Host is not reachable: $errstr ($errno)";
}
```

## Stream контексты

```php
$context = stream_context_create([
    'http' => [
        'method' => 'GET',
        'header' => 'User-Agent: MyApp',
        'timeout' => 30,
        'follow_location' => true,
        'max_redirects' => 5
    ],
    'ssl' => [
        'verify_peer' => true,
        'verify_peer_name' => true,
        'cafile' => '/path/to/ca-bundle.crt'
    ]
]);

$content = file_get_contents('https://example.com', false, $context);
```
