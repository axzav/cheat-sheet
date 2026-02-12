# Integrity Constraints

Ограничения целостности (integrity constraints) — правила, которые обеспечивают корректность и согласованность данных в базе данных. Они предотвращают вставку, обновление или удаление данных, которые нарушают бизнес-логику и структуру базы данных.

## Типы ограничений целостности

### Entity Integrity (Целостность сущности)

Обеспечивает уникальность и идентифицируемость каждой записи в таблице.

**Primary Key**
- Уникально идентифицирует каждую строку
- Не может быть NULL
- Только один на таблицу

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50)
);
```

**Unique Constraint**
- Обеспечивает уникальность значений
- Может быть NULL (обычно только одно NULL значение)
- Может быть несколько на таблицу

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    username VARCHAR(50) UNIQUE
);
```

### Referential Integrity (Ссылочная целостность)

Обеспечивает корректность связей между таблицами через foreign keys.

**Foreign Key**
- Значение должно существовать в связанной таблице
- Может быть NULL (если разрешено)
- Поддерживает каскадные действия

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### Domain Integrity (Доменная целостность)

Обеспечивает корректность значений в столбцах.

**NOT NULL**
- Запрещает NULL значения

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) NOT NULL,
    name VARCHAR(100) NOT NULL
);
```

**CHECK Constraint**
- Проверяет условие перед вставкой/обновлением

```sql
CREATE TABLE products (
    id INT PRIMARY KEY,
    price DECIMAL(10, 2) CHECK (price > 0),
    quantity INT CHECK (quantity >= 0),
    status VARCHAR(20) CHECK (status IN ('active', 'inactive', 'archived'))
);
```

**Data Types**
- Ограничивает тип данных, которые могут быть сохранены

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    age INT CHECK (age >= 0 AND age <= 150)
);
```

**DEFAULT**
- Устанавливает значение по умолчанию

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    status VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Уровни целостности

### Уровень таблицы

Ограничения применяются к отдельным таблицам.

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE NOT NULL,
    age INT CHECK (age >= 18)
);
```

### Уровень базы данных

Ограничения могут ссылаться на несколько таблиц (foreign keys).

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## Создание ограничений

### При создании таблицы

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE NOT NULL,
    age INT CHECK (age >= 0)
);
```

### Именованные ограничения

```sql
CREATE TABLE users (
    id INT,
    email VARCHAR(100),
    age INT,
    CONSTRAINT pk_users PRIMARY KEY (id),
    CONSTRAINT uq_email UNIQUE (email),
    CONSTRAINT chk_age CHECK (age >= 0)
);
```

### Добавление к существующей таблице

```sql
-- Добавить primary key
ALTER TABLE users ADD PRIMARY KEY (id);

-- Добавить unique constraint
ALTER TABLE users ADD CONSTRAINT uq_email UNIQUE (email);

-- Добавить check constraint
ALTER TABLE users ADD CONSTRAINT chk_age CHECK (age >= 0);

-- Добавить foreign key
ALTER TABLE orders ADD CONSTRAINT fk_user 
    FOREIGN KEY (user_id) REFERENCES users(id);
```

## Удаление ограничений

```sql
-- Удалить constraint по имени
ALTER TABLE users DROP CONSTRAINT chk_age;

-- Удалить primary key
ALTER TABLE users DROP PRIMARY KEY;

-- Удалить foreign key
ALTER TABLE orders DROP CONSTRAINT fk_user;
```

## Каскадные действия (Cascade Actions)

### ON DELETE

```sql
-- CASCADE: Удалить связанные записи
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE

-- SET NULL: Установить в NULL
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL

-- RESTRICT: Запретить удаление
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT

-- NO ACTION: Аналогично RESTRICT
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE NO ACTION
```

### ON UPDATE

```sql
-- CASCADE: Обновить связанные записи
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE CASCADE

-- SET NULL: Установить в NULL
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE SET NULL

-- RESTRICT: Запретить обновление
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE RESTRICT
```

## Преимущества ограничений целостности

1. **Защита данных**: Предотвращение некорректных данных на уровне БД
2. **Согласованность**: Обеспечение логической согласованности данных
3. **Документация**: Явное описание правил и связей
4. **Производительность**: Оптимизация запросов за счет информации об ограничениях
5. **Безопасность**: Защита от ошибок приложения

## Соображения

### Производительность

- Ограничения добавляют накладные расходы на INSERT/UPDATE/DELETE
- Проверки выполняются при каждой операции
- Foreign keys требуют проверки связанных таблиц

### Гибкость

- Строгие ограничения могут усложнить миграцию данных
- Иногда требуется временно отключить ограничения
- Не все ограничения поддерживаются всеми СУБД одинаково

### Практические рекомендации

1. Использовать ограничения для критичных правил
2. Балансировать между строгостью и гибкостью
3. Документировать бизнес-правила, реализованные через ограничения
4. Тестировать производительность с ограничениями
5. Планировать стратегию каскадных действий
