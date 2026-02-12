# Composer Commands

```shell
COMPOSER_MEMORY_LIMIT=-1 composer install
```

Describes which packages require the package in param:
```shell
composer why symfony/error-handler
```

```shell
composer show                  # to show list of all installed packages
composer show "symfony/*"      # installed by pattern
composer show "symfony/cache"  # get full package info

composer show guzzlehttp/guzzle --all | grep versions
composer show firebase/php-jwt --all | grep versions

composer require guzzlehttp/guzzle:* --dry-run
composer require google/apiclient:* --dry-run
```
