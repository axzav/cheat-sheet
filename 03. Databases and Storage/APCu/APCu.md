# APCu

APCu — это in-memory key-value хранилище для PHP, работает прямо внутри PHP-процесса.
APCu в PHP-FPM не шарит данные между процессами.

```dockerfile
RUN pecl install apcu-5.1.20 && \
    docker-php-ext-enable apcu
```

```php
apcu_store('token', $token, 300); // TTL = 300 секунд
$token = apcu_fetch('token');
```
