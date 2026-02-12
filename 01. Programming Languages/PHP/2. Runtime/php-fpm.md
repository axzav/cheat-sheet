# PHP-FPM, CGI, FastCGI

PHP может работать в разных режимах взаимодействия с веб-сервером.

## CGI (Common Gateway Interface)

**CGI** - стандартный протокол для запуска внешних программ веб-сервером.

### Особенности
- Каждый запрос запускает новый процесс PHP
- Процесс завершается после обработки запроса
- Высокие накладные расходы на создание процессов
- Низкая производительность

### Использование
```bash
# Apache с mod_cgi
ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
AddHandler cgi-script .cgi .pl .php
```

## FastCGI

**FastCGI** - улучшенная версия CGI, где процесс PHP остается активным между запросами.

### Особенности
- Процесс PHP запускается один раз и обрабатывает множество запросов
- Снижены накладные расходы на создание процессов
- Высокая производительность
- Поддержка пула процессов (process pool)

### Конфигурация Nginx
```nginx
location ~ \.php$ {
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}
```

### Конфигурация Apache
```apache
LoadModule fcgid_module modules/mod_fcgid.so
<Directory /var/www/html>
    Options +ExecCGI
    FcgidWrapper /usr/bin/php-cgi .php
</Directory>
```

## PHP-FPM (FastCGI Process Manager)

**PHP-FPM** - менеджер процессов FastCGI для PHP, предоставляющий расширенные возможности управления.

### Основные возможности
- Управление пулом процессов (worker pool)
- Graceful перезапуск без потери запросов
- Мониторинг и логирование
- Динамическое управление количеством процессов
- Разные пулы для разных сайтов

### Конфигурация php-fpm.conf

```ini
; Основной файл конфигурации
pid = /var/run/php-fpm.pid
error_log = /var/log/php-fpm.log
log_level = notice
emergency_restart_threshold = 10
emergency_restart_interval = 1m
process_control_timeout = 10s
```

### Конфигурация пула (www.conf)

```ini
[www]
user = www-data
group = www-data

; Слушать на сокете или TCP
listen = /var/run/php-fpm.sock
; или
listen = 127.0.0.1:9000

; Режим управления процессами
pm = dynamic  ; dynamic, static, ondemand

; Для dynamic режима
pm.max_children = 50        ; Максимум процессов
pm.start_servers = 10       ; Процессов при старте
pm.min_spare_servers = 5    ; Минимум свободных
pm.max_spare_servers = 20   ; Максимум свободных
pm.max_requests = 500       ; Перезапуск после N запросов

; Для static режима
pm.max_children = 20

; Для ondemand режима
pm.process_idle_timeout = 10s

; Логирование
access.log = /var/log/php-fpm-access.log
access.format = "%R - %u %t \"%m %r%Q%q\" %s %f %{mili}d %{kilo}M %C%%"

; Безопасность
listen.owner = www-data
listen.group = www-data
listen.mode = 0660
```

### Режимы управления процессами

#### Static
- Фиксированное количество процессов
- Подходит для стабильной нагрузки
- Нет накладных расходов на создание процессов

#### Dynamic
- Количество процессов меняется в зависимости от нагрузки
- Оптимальный баланс между производительностью и использованием ресурсов
- Рекомендуется для большинства случаев

#### Ondemand
- Процессы создаются по запросу
- Подходит для низкой нагрузки
- Экономия ресурсов

### Управление PHP-FPM

```bash
# Запуск
sudo systemctl start php-fpm
# или
sudo service php-fpm start

# Остановка
sudo systemctl stop php-fpm

# Перезапуск (graceful)
sudo systemctl reload php-fpm
# или
sudo kill -USR2 $(cat /var/run/php-fpm.pid)

# Статус
sudo systemctl status php-fpm

# Проверка конфигурации
sudo php-fpm -t
```

### Мониторинг

```bash
# Статус процессов
sudo systemctl status php-fpm

# Логи
tail -f /var/log/php-fpm.log
tail -f /var/log/php-fpm-access.log

# Статус пула через статус-страницу
; В конфигурации пула
pm.status_path = /status

; В Nginx
location ~ ^/(status|ping)$ {
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}
```

### Ping endpoint

```ini
; В конфигурации пула
ping.path = /ping
```

### Оптимизация производительности

```ini
; Увеличить лимит открытых файлов
rlimit_files = 65535

; Отключить логирование в production
access.log = /dev/null

; Настройка для высоких нагрузок
pm.max_children = 100
pm.start_servers = 20
pm.min_spare_servers = 10
pm.max_spare_servers = 30
pm.max_requests = 1000
```

### Безопасность

```ini
; Отдельный пул для каждого сайта
[site1]
user = site1
group = site1
listen = /var/run/php-fpm-site1.sock

[site2]
user = site2
group = site2
listen = /var/run/php-fpm-site2.sock

; Ограничение доступа к статусу
listen.allowed_clients = 127.0.0.1
```

## Сравнение режимов

| Режим | Производительность | Использование памяти | Сложность настройки |
|-------|-------------------|---------------------|-------------------|
| CGI | Низкая | Низкое | Простая |
| FastCGI | Средняя | Среднее | Средняя |
| PHP-FPM | Высокая | Высокое | Сложная |

## Выбор режима

- **CGI**: Только для разработки или очень специфичных случаев
- **FastCGI**: Для простых конфигураций без управления процессами
- **PHP-FPM**: Рекомендуется для production, особенно с Nginx
