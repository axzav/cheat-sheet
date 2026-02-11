# Сессии

Сессии позволяют хранить данные между HTTP-запросами на сервере.

## Базовое использование

```php
// Запуск сессии
session_start();

// Установка значения
$_SESSION['username'] = 'user123';

// Получение значения
echo $_SESSION['username'];

// Удаление значения
unset($_SESSION['username']);

// Уничтожение сессии
session_destroy();
```

## Проблемы с файловым хранилищем

**Важно:** Сессии нельзя хранить в файлах при использовании в веб-приложениях, так как при одновременном обращении нескольких процессов к сессии может возникнуть deadlock.

## Кастомные обработчики сессий

### Хранение в базе данных

```php
class MySQLSessionHandler implements SessionHandlerInterface
{
    private $pdo;

    public function __construct(PDO $pdo)
    {
        $this->pdo = $pdo;
    }

    public function open($savePath, $sessionName): bool
    {
        return true;
    }

    public function close(): bool
    {
        return true;
    }

    public function read($id): string
    {
        $stmt = $this->pdo->prepare("SELECT data FROM sessions WHERE id = :id");
        $stmt->bindParam(':id', $id, PDO::PARAM_STR);
        $stmt->execute();

        if ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
            return $row['data'];
        }

        return '';
    }

    public function write($id, $data): bool
    {
        $stmt = $this->pdo->prepare(
            "REPLACE INTO sessions (id, data, timestamp) VALUES (:id, :data, NOW())"
        );
        $stmt->bindParam(':id', $id, PDO::PARAM_STR);
        $stmt->bindParam(':data', $data, PDO::PARAM_STR);
        return $stmt->execute();
    }

    public function destroy($id): bool
    {
        $stmt = $this->pdo->prepare("DELETE FROM sessions WHERE id = :id");
        $stmt->bindParam(':id', $id, PDO::PARAM_STR);
        return $stmt->execute();
    }

    public function gc($maxlifetime): bool
    {
        $stmt = $this->pdo->prepare(
            "DELETE FROM sessions WHERE timestamp < NOW() - INTERVAL :maxlifetime SECOND"
        );
        $stmt->bindParam(':maxlifetime', $maxlifetime, PDO::PARAM_INT);
        return $stmt->execute();
    }
}

// Использование
$pdo = new PDO('mysql:host=localhost;dbname=test', 'username', 'password');
$handler = new MySQLSessionHandler($pdo);
session_set_save_handler($handler, true);
session_start();
```

### Хранение в Redis

```php
ini_set('session.save_handler', 'redis');
ini_set('session.save_path', 'tcp://127.0.0.1:6379');

session_start();
$_SESSION['username'] = 'example_user';
```

### Хранение в Memcached

```php
ini_set('session.save_handler', 'memcached');
ini_set('session.save_path', '127.0.0.1:11211');

session_start();
$_SESSION['username'] = 'example_user';
```

## Настройки сессий

```php
// Время жизни сессии (в секундах)
ini_set('session.gc_maxlifetime', 3600);

// Вероятность запуска сборщика мусора (1/100)
ini_set('session.gc_probability', 1);
ini_set('session.gc_divisor', 100);

// Имя cookie сессии
ini_set('session.name', 'PHPSESSID');

// Путь для cookie
ini_set('session.cookie_path', '/');

// Домен для cookie
ini_set('session.cookie_domain', '');

// Только HTTPS
ini_set('session.cookie_secure', 1);

// HttpOnly флаг
ini_set('session.cookie_httponly', 1);
```
