# OPCache & JIT

Механизмы оптимизации производительности PHP на уровне выполнения кода.

## OPCache

**OPCache** (Opcode Cache) - кэширует скомпилированный байт-код PHP, избегая повторной компиляции при каждом запросе.

### Принцип работы

1. PHP компилирует исходный код в байт-код (opcodes)
2. OPCache сохраняет байт-код в памяти
3. При повторном запросе используется кэшированный байт-код
4. Экономия времени на компиляции

### Включение OPCache

```ini
; php.ini
zend_extension=opcache
opcache.enable=1
opcache.enable_cli=0  ; Обычно отключен для CLI
```

### Основные настройки

```ini
; Размер памяти для кэша (в мегабайтах)
opcache.memory_consumption=128

; Максимальное количество скриптов в кэше
opcache.max_accelerated_files=10000

; Проверка изменений файлов
opcache.validate_timestamps=1  ; 1 для dev, 0 для production
opcache.revalidate_freq=2      ; Проверка каждые N секунд

; Оптимизация
opcache.optimization_level=0x7FFFBFFF
opcache.fast_shutdown=1

; Логирование
opcache.error_log=/var/log/php-opcache.log
opcache.log_verbosity_level=1
```

### Проверка статуса OPCache

```php
// Информация о OPCache
$status = opcache_get_status();
print_r($status);

// Статистика
echo "Hit rate: " . ($status['opcache_statistics']['opcache_hit_rate']) . "%\n";
echo "Cached scripts: " . $status['opcache_statistics']['num_cached_scripts'] . "\n";
echo "Memory used: " . round($status['memory_usage']['used_memory'] / 1024 / 1024, 2) . " MB\n";
```

### Управление кэшем

```php
// Инвалидация кэша для конкретного файла
opcache_invalidate('/path/to/file.php', true);

// Инвалидация всех файлов
opcache_reset();

// Проверка, кэширован ли файл
if (opcache_is_script_cached('/path/to/file.php')) {
    echo "File is cached\n";
}
```

### Настройки для production

```ini
; Отключить проверку изменений (максимальная производительность)
opcache.validate_timestamps=0

; Увеличить размер памяти
opcache.memory_consumption=256

; Увеличить количество файлов
opcache.max_accelerated_files=20000

; Включить быстрый shutdown
opcache.fast_shutdown=1

; Оптимизация строк
opcache.interned_strings_buffer=16
```

### Настройки для development

```ini
; Включить проверку изменений
opcache.validate_timestamps=1
opcache.revalidate_freq=0  ; Проверка при каждом запросе

; Включить для CLI (для тестирования)
opcache.enable_cli=1
```

### Проблемы и решения

#### Кэш не обновляется
```ini
; Убедитесь, что включена проверка
opcache.validate_timestamps=1
opcache.revalidate_freq=0
```

#### Недостаточно памяти
```ini
; Увеличьте размер
opcache.memory_consumption=256
```

#### Файлы не кэшируются
```php
// Проверьте лимит
$config = opcache_get_configuration();
echo "Max files: " . $config['directives']['opcache.max_accelerated_files'] . "\n";
```

## JIT (Just-In-Time Compilation)

**JIT** - компиляция байт-кода в машинный код во время выполнения для повышения производительности.

### Включение JIT

```ini
; JIT доступен с PHP 8.0
opcache.jit_buffer_size=256M
opcache.jit=tracing  ; или 'function', 'on', 'off'
```

### Режимы JIT

#### Tracing (рекомендуется)
```ini
opcache.jit=tracing
```
- Оптимизирует "горячие" участки кода (hot paths)
- Лучшая производительность для большинства случаев
- Анализирует выполнение и компилирует часто используемые пути

#### Function
```ini
opcache.jit=function
```
- Компилирует отдельные функции
- Меньше оптимизаций, но быстрее компиляция

#### Off
```ini
opcache.jit=off
```
- JIT отключен

### Настройки JIT

```ini
; Размер буфера для JIT кода
opcache.jit_buffer_size=256M

; Уровень оптимизации (0-4, 4 = максимум)
opcache.jit=1255  ; tracing mode с максимальной оптимизацией

; Формат: CRTO
; C - CPU-специфичные оптимизации (0-1)
; R - регистровая аллокация (0-4)
; T - оптимизация циклов (0-5)
; O - оптимизация кода (0-4)

; Примеры:
opcache.jit=1255  ; tracing, максимум оптимизаций
opcache.jit=1205  ; tracing, средние оптимизации
opcache.jit=function  ; function mode
```

### Когда JIT полезен

- Вычислительно интенсивные задачи
- Математические операции
- Обработка больших массивов
- Циклы с большим количеством итераций
- Код с предсказуемыми путями выполнения

### Когда JIT не помогает

- I/O операции (запросы к БД, файлы, сеть)
- Простые веб-приложения с низкой вычислительной нагрузкой
- Код с частыми вызовами внешних функций

### Проверка работы JIT

```php
// Статус OPCache (включает информацию о JIT)
$status = opcache_get_status();

if (isset($status['jit'])) {
    echo "JIT enabled: " . ($status['jit']['enabled'] ? 'Yes' : 'No') . "\n";
    echo "JIT buffer size: " . $status['jit']['buffer_size'] . "\n";
    echo "JIT compiled functions: " . $status['jit']['compiled_function_count'] . "\n";
    echo "JIT compiled traces: " . $status['jit']['compiled_trace_count'] . "\n";
}
```

### Оптимизация JIT

```ini
; Для максимальной производительности
opcache.jit_buffer_size=512M
opcache.jit=tracing
opcache.jit=1255  ; Максимальные оптимизации

; Для баланса производительности и памяти
opcache.jit_buffer_size=256M
opcache.jit=tracing
opcache.jit=1205  ; Средние оптимизации
```

### Совместная работа OPCache и JIT

1. **OPCache** кэширует байт-код
2. **JIT** компилирует часто используемый байт-код в машинный код
3. Результат: максимальная производительность

### Рекомендации

#### Production
```ini
opcache.enable=1
opcache.memory_consumption=256
opcache.max_accelerated_files=20000
opcache.validate_timestamps=0
opcache.jit_buffer_size=256M
opcache.jit=tracing
opcache.jit=1255
```

#### Development
```ini
opcache.enable=1
opcache.validate_timestamps=1
opcache.revalidate_freq=0
opcache.jit=off  ; Обычно не нужен в dev
```

### Мониторинг

```php
function getOpCacheInfo() {
    $status = opcache_get_status();
    $config = opcache_get_configuration();
    
    return [
        'enabled' => $status['opcache_enabled'],
        'cache_full' => $status['cache_full'],
        'hit_rate' => round($status['opcache_statistics']['opcache_hit_rate'], 2),
        'memory_used' => round($status['memory_usage']['used_memory'] / 1024 / 1024, 2),
        'memory_free' => round($status['memory_usage']['free_memory'] / 1024 / 1024, 2),
        'cached_scripts' => $status['opcache_statistics']['num_cached_scripts'],
        'jit_enabled' => isset($status['jit']) && $status['jit']['enabled'],
    ];
}

print_r(getOpCacheInfo());
```
