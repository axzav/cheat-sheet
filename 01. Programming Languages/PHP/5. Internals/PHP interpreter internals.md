# PHP Interpreter Internals

Внутреннее устройство интерпретатора PHP и процесс выполнения кода.

## Архитектура интерпретатора

### Основные компоненты

1. **Lexer (Лексер)** - разбор исходного кода на токены
2. **Parser (Парсер)** - построение Abstract Syntax Tree (AST)
3. **Compiler (Компилятор)** - генерация байт-кода (opcodes)
4. **Zend VM** - выполнение байт-кода
5. **JIT Compiler** - компиляция в машинный код (PHP 8.0+)

## Процесс компиляции

### Этап 1: Лексический анализ

Исходный код разбивается на токены (tokens).

```php
// Исходный код
$a = 1 + 2;

// Токены
T_VARIABLE($a)
T_WHITESPACE
=
T_WHITESPACE
T_LNUMBER(1)
T_WHITESPACE
+
T_WHITESPACE
T_LNUMBER(2)
;
```

### Этап 2: Парсинг

Токены преобразуются в Abstract Syntax Tree (AST).

```
AST Node: ASSIGN
├── Variable: $a
└── AST Node: ADD
    ├── Constant: 1
    └── Constant: 2
```

### Этап 3: Компиляция в байт-код

AST преобразуется в опкоды (opcodes) для Zend VM.

```php
// Байт-код для $a = 1 + 2;
ASSIGN $a, ~0
ADD ~0, 1, 2
```

### Этап 4: Оптимизация

Байт-код оптимизируется:
- Удаление мертвого кода
- Свертка констант
- Инлайнинг функций

## Zend Virtual Machine

### Выполнение опкодов

Zend VM выполняет опкоды последовательно, используя диспетчер опкодов (opcode dispatcher).

### Диспетчер опкодов

```c
// Упрощенная версия
while (1) {
    opcode = *opline;
    handler = opcode->handler;
    handler(opcode);
    opline++;
}
```

### Обработчики опкодов

Каждый опкод имеет обработчик (handler), который выполняет соответствующую операцию.

```c
// Пример обработчика ADD
ZEND_ADD_SPEC_CONST_CONST_HANDLER() {
    result = op1->value.lval + op2->value.lval;
    ZVAL_LONG(result, result);
}
```

## JIT Compilation (PHP 8.0+)

### Принцип работы

1. **Профилирование** - отслеживание "горячих" участков кода
2. **Компиляция** - преобразование байт-кода в машинный код
3. **Выполнение** - выполнение скомпилированного машинного кода

### Tracing JIT

JIT компилирует "трассировки" - последовательности опкодов, которые часто выполняются.

```php
// Пример трассировки
for ($i = 0; $i < 1000000; $i++) {
    $result += $i * 2;  // Эта трассировка будет скомпилирована
}
```

### Условия для JIT компиляции

- Код выполняется многократно
- Предсказуемый путь выполнения
- Минимум вызовов внешних функций

## Управление памятью

### Zend Memory Manager

Система управления памятью в PHP использует несколько стратегий:

#### Reference Counting

```php
$a = "string";  // zval: refcount = 1
$b = $a;        // zval: refcount = 2 (копирование не происходит)
unset($a);      // zval: refcount = 1
unset($b);      // zval: refcount = 0 -> память освобождена
```

#### Copy-on-Write (COW)

```php
$a = [1, 2, 3];     // Создается массив
$b = $a;            // Копирование не происходит, только увеличивается refcount
$b[] = 4;           // Теперь создается копия, так как $a и $b должны быть разными
```

#### Garbage Collection

Циклические ссылки обрабатываются сборщиком мусора:

```php
$a = new stdClass();
$b = new stdClass();
$a->ref = $b;
$b->ref = $a;  // Циклическая ссылка
unset($a, $b); // GC обнаружит и освободит память
```

## Типы данных и Zval

### Zval структура (PHP 7+)

```c
struct _zval_struct {
    zend_value value;        // Значение (union всех типов)
    zend_uchar type;         // Тип данных
    zend_uchar type_flags;   // Флаги типа
    zend_uchar const_flags;  // Константные флаги
    zend_uchar reserved;     // Зарезервировано
    union {
        uint32_t var_flags;
        uint32_t next;       // Для хеш-таблиц
        uint32_t cache_slot;
        uint32_t lineno;
        uint32_t num_args;
        uint32_t fe_pos;
    } u2;
};
```

### Типы данных

- **IS_UNDEF** - неопределенное значение
- **IS_NULL** - null
- **IS_FALSE** - false (PHP 8.0+)
- **IS_TRUE** - true (PHP 8.0+)
- **IS_LONG** - integer
- **IS_DOUBLE** - float
- **IS_STRING** - string
- **IS_ARRAY** - array
- **IS_OBJECT** - object
- **IS_RESOURCE** - resource (устарело в PHP 8.0)
- **IS_REFERENCE** - reference
- **IS_CONSTANT_AST** - константное AST

## Hash Tables (Массивы)

PHP массивы реализованы как хеш-таблицы с упорядоченными элементами.

### Структура HashTable

```c
typedef struct _zend_array HashTable;

struct _zend_array {
    zend_refcounted_h gc;
    union {
        struct {
            zend_uchar flags;
            zend_uchar nApplyCount;
            zend_uchar nIteratorsCount;
            zend_uchar consistency;
        } v;
        uint32_t flags;
    } u;
    uint32_t nTableMask;
    Bucket *arData;           // Массив bucket'ов
    uint32_t nNumUsed;        // Используемые элементы
    uint32_t nNumOfElements;  // Количество элементов
    uint32_t nTableSize;      // Размер таблицы (степень 2)
    uint32_t nInternalPointer;
    zend_long nNextFreeElement;
    dtor_func_t pDestructor;
};
```

### Bucket структура

```c
typedef struct _Bucket {
    zval val;                 // Значение
    zend_ulong h;             // Хеш ключа
    zend_string *key;         // Ключ (для строковых ключей)
} Bucket;
```

### Особенности

- **Упорядоченность** - элементы сохраняют порядок вставки
- **Быстрый доступ** - O(1) в среднем случае
- **Динамическое расширение** - размер увеличивается при необходимости

## Объекты и классы

### Zend Object структура

```c
struct _zend_object {
    zend_refcounted_h gc;
    uint32_t handle;
    zend_class_entry *ce;     // Класс
    const zend_object_handlers *handlers;
    HashTable *properties;     // Свойства объекта
    zval properties_table[1]; // Встроенная таблица свойств
};
```

### Классы (zend_class_entry)

```c
struct _zend_class_entry {
    char type;
    zend_string *name;
    struct _zend_class_entry *parent;
    int refcount;
    uint32_t ce_flags;
    
    HashTable function_table;  // Методы
    HashTable properties_info; // Информация о свойствах
    zval default_properties_table;
    zval *default_properties_static_table;
    
    zend_object_handlers *default_object_handlers;
    zend_object_iterator_funcs *get_iterator;
    
    // ... другие поля
};
```

## Функции и методы

### Zend Function структура

```c
typedef union _zend_function {
    zend_uchar type;
    
    struct {
        zend_uchar type;
        zend_uchar arg_flags[3];
        uint32_t fn_flags;
        zend_string *function_name;
        zend_class_entry *scope;
        zend_function *prototype;
        uint32_t num_args;
        uint32_t required_num_args;
        zend_arg_info *arg_info;
    } common;
    
    zend_op_array op_array;  // Для обычных функций
    zend_internal_function internal_function; // Для внутренних функций
} zend_function;
```

## Производительность

### Оптимизации PHP 7+

- **Улучшенная структура zval** - меньше аллокаций памяти
- **Улучшенный HashTable** - более эффективное использование памяти
- **Оптимизация опкодов** - более эффективные обработчики
- **JIT компиляция** - компиляция в машинный код

### Профилирование

```php
// Использование opcache_get_status для анализа
$status = opcache_get_status();
print_r($status['opcache_statistics']);
```

## Ссылки

- [Как работает PHP движок](https://thephp.website/en/issue/how-does-php-engine-actually-work/)
- [PHP 8 JIT](https://thephp.website/en/issue/php-8-jit/)
- [PHP 8.0 JIT документация](https://php.watch/versions/8.0/JIT)
- [Внутреннее устройство PHP интерпретатора](https://habr.com/ru/company/otus/blog/509598/)
