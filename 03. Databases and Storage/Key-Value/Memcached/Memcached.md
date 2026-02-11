# Memcached

Memcached - `in-memory` хранилище в формате `key-value` .
Ненадежное - при перезапуске теряется вся база.
Очень простое и быстрое хранилище.

Можно использовать для хранения некритичных данных:
- сессии
- кеши


```dockerfile
RUN apt-get update && apt-get install -y memcached libmemcached-dev
RUN pecl install memcached-3.1.5 && docker-php-ext-enable memcached
```


```php
$mem = new Memcached();
$mem->addServer('memcached', 11211);

$mem->set('foo', 'bar');
echo $mem->get('foo');
```
