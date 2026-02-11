# PHP Hacks
https://medium.com/@frontman/fun-php-1-19ad75ee78bb
https://medium.com/@frontman/fun-js-php-2-434b02ea4894
https://medium.com/@frontman/fun-php-3-3ad1d30c993e
https://medium.com/@frontman/fun-php-4-97ec3347470c
https://medium.com/@frontman/php-access-to-private-and-protected-b1028b974169


Установлен ли
```php
abstract class CommonTokenRegistry
{
    public function __construct(private readonly RedisCacheInterface $cache)
    {
        try {
            static::TOKEN_PATTERN;
        } catch (\Throwable $e) {
            throw new RuntimeException(sprintf('Parent class "%s" must define "TOKEN_PATTERN" const.', \get_class($this)));
        }
    }
```