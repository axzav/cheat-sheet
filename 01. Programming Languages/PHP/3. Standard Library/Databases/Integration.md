# Интеграция с базами данных

## PDO (PHP Data Objects)

PDO предоставляет единый интерфейс для работы с различными базами данных.

### Подключение

```php
$dsn = "mysql:host=localhost;dbname=mydb;charset=utf8mb4";
$options = [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES   => false,
];

try {
    $pdo = new PDO($dsn, $username, $password, $options);
} catch (PDOException $e) {
    throw new PDOException($e->getMessage(), (int)$e->getCode());
}
```

### Подготовленные запросы

```php
// Именованные плейсхолдеры
$stmt = $pdo->prepare('SELECT * FROM users WHERE id = :id AND status = :status');
$stmt->execute(['id' => 1, 'status' => 'active']);
$user = $stmt->fetch();

// Позиционные плейсхолдеры
$stmt = $pdo->prepare('SELECT * FROM users WHERE id = ? AND status = ?');
$stmt->execute([1, 'active']);
$user = $stmt->fetch();
```

### Выборка данных

```php
// Одна строка
$user = $pdo->query('SELECT * FROM users WHERE id = 1')->fetch();

// Все строки
$users = $pdo->query('SELECT * FROM users')->fetchAll();

// Одно значение
$count = $pdo->query('SELECT COUNT(*) FROM users')->fetchColumn();
```

### Вставка данных

```php
$stmt = $pdo->prepare('INSERT INTO users (name, email) VALUES (:name, :email)');
$stmt->execute(['name' => 'John', 'email' => 'john@example.com']);
$userId = $pdo->lastInsertId();
```

### Транзакции

```php
$pdo->beginTransaction();
try {
    $pdo->exec('INSERT INTO users ...');
    $pdo->exec('INSERT INTO profiles ...');
    $pdo->commit();
} catch (Exception $e) {
    $pdo->rollBack();
    throw $e;
}
```

## MySQLi

### Процедурный стиль

```php
$mysqli = mysqli_connect('localhost', 'user', 'password', 'database');

$result = mysqli_query($mysqli, 'SELECT * FROM users');
while ($row = mysqli_fetch_assoc($result)) {
    // Обработка строки
}

mysqli_close($mysqli);
```

### Объектно-ориентированный стиль

```php
$mysqli = new mysqli('localhost', 'user', 'password', 'database');

$stmt = $mysqli->prepare('SELECT * FROM users WHERE id = ?');
$stmt->bind_param('i', $id);
$stmt->execute();
$result = $stmt->get_result();
$user = $result->fetch_assoc();

$mysqli->close();
```

## ORM библиотеки

### Doctrine

```php
use Doctrine\ORM\EntityManager;

$user = $entityManager->find(User::class, $id);
$user->setName('New Name');
$entityManager->flush();
```

### Eloquent (Laravel)

```php
$user = User::find($id);
$user->name = 'New Name';
$user->save();
```

## Управление соединениями

### Connection Pooling

Для высоконагруженных приложений используйте пулы соединений или менеджеры соединений.

### Закрытие соединений

Всегда закрывайте соединения после использования:

```php
$pdo = null; // PDO автоматически закроет соединение
$mysqli->close(); // MySQLi требует явного закрытия
```

## Безопасность

1. **Всегда используйте подготовленные запросы** - защита от SQL инъекций
2. **Валидируйте входные данные** - проверяйте типы и значения
3. **Используйте принцип наименьших привилегий** - ограничьте права пользователя БД
4. **Храните пароли безопасно** - используйте `password_hash()` и `password_verify()`
5. **Логируйте ошибки** - но не показывайте их пользователю
