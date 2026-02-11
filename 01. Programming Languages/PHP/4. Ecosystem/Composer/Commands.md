# Composer Commands


COMPOSER_MEMORY_LIMIT=-1 composer install

Describes which packages requires the package in param:
```shell
composer why symfony/error-handler
```

```shell
composer show                  # to show list of all installed packages
composer show "symfony/*"      # installed by pattern
composer show "symfony/cache"  # get full package info
```

composer show guzzlehttp/guzzle --all | grep versions

composer require guzzlehttp/guzzle:* --dry-run



composer require google/apiclient:* --dry-run


composer show firebase/php-jwt --all | grep versions

firebase/php-jwt