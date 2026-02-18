```shell
apt-get install php-redis
```

```dockerfile
RUN pecl install redis && docker-php-ext-enable redis
```

Все значения по умолчанию строки. Если установить значение как число в php то при получении значения это все равно будет string

```php
$redis = new Redis();
$redis->connect('redis', 6379);

$redis->set('foo', 'bar');
echo $redis->get('foo');
```
