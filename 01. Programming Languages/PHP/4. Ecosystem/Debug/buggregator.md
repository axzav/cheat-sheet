## Buggregator

https://buggregator.dev/
https://docs.buggregator.dev/getting-started.html
https://docs.buggregator.dev/config/inspector.html

Чтобы работало в симфони:
```shell
composer require --dev symfony/var-dumper
```

```dotenv
VAR_DUMPER_FORMAT=server
VAR_DUMPER_SERVER="buggregator:9912"
MONOLOG_SOCKET_HOST="buggregator:9913"
```

http://127.0.0.1:8000/#/