# Blackfire

Blackfire — инструмент для профилирования и мониторинга производительности PHP приложений.

## Установка

### Через пакетный менеджер

```bash
# Ubuntu/Debian
curl -s https://packagecloud.io/install/repositories/blackfire/blackfire/script.deb.sh | sudo bash
sudo apt-get install blackfire-agent blackfire-php

# CentOS/RHEL
curl -s https://packagecloud.io/install/repositories/blackfire/blackfire/script.rpm.sh | sudo bash
sudo yum install blackfire-agent blackfire-php
```

### Через PECL

```bash
pecl install blackfire
```

## Конфигурация

```ini
; php.ini
extension=blackfire.so
blackfire.agent_socket = unix:///var/run/blackfire/agent.sock
blackfire.agent_timeout = 0.25
blackfire.log_file = /var/log/blackfire.log
blackfire.log_level = 1
```

## Использование

### CLI профилирование

```bash
# Профилирование команды
blackfire run php script.php

# С конфигурацией
blackfire --config=production run php script.php
```

### Веб-профилирование

```bash
# Установка расширения браузера
# Blackfire автоматически профилирует запросы при установленном расширении
```

### Программное профилирование

```php
<?php
use Blackfire\Client;
use Blackfire\Profile\Configuration;

$client = new Client();
$config = new Configuration();
$config->setTitle('My Profile');

$probe = $client->createProbe($config);

// Код для профилирования
// ...

$client->endProbe($probe);
```

## Интеграция с фреймворками

### Symfony

```bash
composer require --dev blackfire/php-sdk
```

```php
<?php
// config/packages/blackfire.yaml
blackfire:
    enabled: true
    server_id: '%env(BLACKFIRE_SERVER_ID)%'
    server_token: '%env(BLACKFIRE_SERVER_TOKEN)%'
```

### Laravel

```bash
composer require --dev blackfire/php-sdk
```

## Анализ производительности

Blackfire предоставляет:

- **Call Graph** — визуализация вызовов функций
- **Timeline** — временная шкала выполнения
- **Metrics** — метрики производительности
- **Recommendations** — рекомендации по оптимизации

## Сравнение профилей

```bash
# Сравнить два профиля
blackfire compare profile1.json profile2.json
```

## CI/CD интеграция

```yaml
# .github/workflows/blackfire.yml
- name: Blackfire
  run: |
    blackfire --env=ci run php vendor/bin/phpunit
```

## Ссылки

- https://www.blackfire.io/
- https://blackfire.io/docs
