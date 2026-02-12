# PHP Core

Внутреннее устройство PHP и виртуальной машины Zend.

## Zend Engine

**Zend Engine** - ядро интерпретатора PHP, отвечающее за выполнение кода.

### Компоненты

- **Zend VM** (Virtual Machine) - виртуальная машина для выполнения байт-кода
- **Zend Compiler** - компилятор исходного кода в байт-код
- **Zend Memory Manager** - управление памятью
- **Zend API** - API для расширений

## Процесс выполнения PHP кода

1. **Лексический анализ (Lexing)**
   - Разбор исходного кода на токены
   - Создание Abstract Syntax Tree (AST)

2. **Компиляция**
   - Преобразование AST в байт-код (opcodes)
   - Оптимизация байт-кода

3. **Кэширование (OPCache)**
   - Сохранение байт-кода в памяти
   - Избежание повторной компиляции

4. **Выполнение (Zend VM)**
   - Интерпретация байт-кода
   - JIT компиляция (опционально)

## Zend Virtual Machine

### Опкоды (Opcodes)

Байт-код PHP состоит из опкодов - инструкций для виртуальной машины.

```php
// Пример: $a = 1 + 2;
// Генерирует опкоды:
// ASSIGN, ADD, CONST
```

### Типы опкодов

- **Арифметические**: ADD, SUB, MUL, DIV
- **Логические**: AND, OR, NOT
- **Управление потоком**: JMP, JMPZ, JMPNZ
- **Работа с переменными**: ASSIGN, FETCH, CONCAT
- **Вызовы функций**: DO_FCALL, DO_ICALL

### Структура опкода

```c
struct _zend_op {
    const void *handler;      // Обработчик опкода
    znode_op op1;             // Операнд 1
    znode_op op2;             // Операнд 2
    znode_op result;          // Результат
    uint32_t extended_value;  // Дополнительное значение
    uint32_t lineno;          // Номер строки
    zend_uchar opcode;        // Тип опкода
    zend_uchar op1_type;      // Тип операнда 1
    zend_uchar op2_type;      // Тип операнда 2
    zend_uchar result_type;   // Тип результата
};
```

## Управление памятью

### Zend Memory Manager (ZMM)

Система управления памятью в PHP.

### Особенности

- **Авomatic garbage collection** - автоматическая сборка мусора
- **Reference counting** - подсчет ссылок для оптимизации
- **Memory pools** - пулы памяти для эффективного выделения

### Reference Counting

```php
$a = "string";  // refcount = 1
$b = $a;        // refcount = 2
unset($a);      // refcount = 1
unset($b);      // refcount = 0, память освобождена
```

### Copy-on-Write (COW)

```php
$a = [1, 2, 3];
$b = $a;        // Копирование не происходит, только ссылка
$b[] = 4;       // Теперь создается копия
```

## Типы данных (Zend Types)

### Zval структура

```c
struct _zval_struct {
    zend_value value;        // Значение
    union {
        struct {
            ZEND_ENDIAN_LOHI_4(
                zend_uchar type,         // Тип данных
                zend_uchar type_flags,   // Флаги типа
                zend_uchar const_flags,  // Константные флаги
                zend_uchar reserved)     // Зарезервировано
        } v;
        uint32_t type_info;
    } u1;
    union {
        uint32_t var_flags;
        uint32_t next;       // Для хеш-таблиц
        uint32_t cache_slot; // Для кэширования
        uint32_t lineno;     // Номер строки
        uint32_t num_args;   // Количество аргументов
        uint32_t fe_pos;     // Позиция в foreach
        uint32_t fe_iter_idx;
    } u2;
};
```

### Типы данных PHP 8

- IS_UNDEF
- IS_NULL
- IS_FALSE
- IS_TRUE
- IS_LONG (int)
- IS_DOUBLE (float)
- IS_STRING
- IS_ARRAY
- IS_OBJECT
- IS_RESOURCE
- IS_REFERENCE
- IS_CONSTANT_AST

## Hash Tables

PHP использует хеш-таблицы для массивов и объектов.

### Структура

```c
typedef struct _zend_array HashTable;

struct _zend_array {
    zend_refcounted_h gc;
    union {
        struct {
            ZEND_ENDIAN_LOHI_4(
                zend_uchar    flags,
                zend_uchar    nApplyCount,
                zend_uchar    nIteratorsCount,
                zend_uchar    consistency)
        } v;
        uint32_t flags;
    } u;
    uint32_t          nTableMask;
    Bucket           *arData;      // Массив bucket'ов
    uint32_t          nNumUsed;    // Используемые элементы
    uint32_t          nNumOfElements; // Количество элементов
    uint32_t          nTableSize;  // Размер таблицы
    uint32_t          nInternalPointer;
    zend_long         nNextFreeElement;
    dtor_func_t       pDestructor;
};
```

## Расширения PHP

### Zend API

API для создания расширений PHP.

### Типы расширений

- **Zend Extension** - расширения ядра
- **PHP Extension** - обычные расширения

### Структура расширения

```c
// php_myextension.h
ZEND_BEGIN_MODULE_GLOBALS(myextension)
    long my_setting;
ZEND_END_MODULE_GLOBALS(myextension)

// myextension.c
ZEND_DECLARE_MODULE_GLOBALS(myextension)

PHP_MINIT_FUNCTION(myextension) {
    // Инициализация модуля
    return SUCCESS;
}

PHP_MSHUTDOWN_FUNCTION(myextension) {
    // Завершение модуля
    return SUCCESS;
}

zend_module_entry myextension_module_entry = {
    STANDARD_MODULE_HEADER,
    "myextension",
    NULL,
    PHP_MINIT(myextension),
    PHP_MSHUTDOWN(myextension),
    NULL,
    NULL,
    NULL,
    "1.0.0",
    STANDARD_MODULE_PROPERTIES
};
```

## Ссылки

- Дмитрий Стогов - разработчик Zend VM, множество видео на YouTube
- [Устройство виртуальной машины Zend](https://habr.com/ru/company/badoo/blog/327068/)
- [Добавление нового оператора в язык](https://habr.com/ru/company/vk/blog/276331/)
- [Написание структур данных](https://habr.com/ru/post/260983/)
