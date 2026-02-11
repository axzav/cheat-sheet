# PHP PDO (3rd party connections?)

Вы можете написать простой скрипт на PHP, который будет использовать PDO (PHP Data Objects) для установления соединения с базой данных MySQL или MariaDB. Например, такой скрипт может выглядеть так:

php
Copy code
<?php

$host = 'db'; // имя хоста Docker
$db   = 'canaryBay'; // имя базы данных
$user = 'canaryBayUser'; // имя пользователя
$pass = 'canaryBayPassword'; // пароль
$charset = 'utf8mb4'; // набор символов

$dsn = "mysql:host=$host;dbname=$db;charset=$charset";
$options = [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES   => false,
];

try {
    $pdo = new PDO($dsn, $user, $pass, $options);
    echo "Соединение с базой данных установлено!";
} catch (\PDOException $e) {
    throw new \PDOException($e->getMessage(), (int)$e->getCode());
}

?>
Этот скрипт попытается установить соединение с базой данных с помощью данных, которые вы указали. Если соединение будет установлено успешно, он выведет сообщение "Соединение с базой данных установлено!". Если при установке соединения возникнет ошибка, он выдаст исключение с сообщением об ошибке.

Примечание: замените значения переменных $host, $db, $user, $pass и $charset на соответствующие значения для вашей базы данных.