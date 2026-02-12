# Foreign Keys

Foreign key (внешний ключ) — ограничение, обеспечивающее ссылочную целостность (referential integrity) между таблицами. Гарантирует, что значения в одной таблице должны существовать в другой таблице.

## Создание Foreign Keys

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## Инлайн определение

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT REFERENCES users(id)
);
```

## Именованный Foreign Key

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    CONSTRAINT fk_user
        FOREIGN KEY (user_id)
        REFERENCES users(id)
);
```

## Добавление Foreign Key к существующей таблице

```sql
ALTER TABLE orders
ADD CONSTRAINT fk_user
FOREIGN KEY (user_id) REFERENCES users(id);
```

## Действия Foreign Key

### ON DELETE

```sql
-- CASCADE: Удалить связанные строки
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE

-- SET NULL: Установить foreign key в NULL
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL

-- RESTRICT: Запретить удаление (по умолчанию)
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE RESTRICT

-- NO ACTION: Аналогично RESTRICT
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE NO ACTION
```

### ON UPDATE

```sql
-- CASCADE: Обновить foreign key при изменении primary key
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE CASCADE

-- SET NULL: Установить в NULL при изменении primary key
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE SET NULL

-- RESTRICT: Запретить обновление (по умолчанию)
FOREIGN KEY (user_id) REFERENCES users(id) ON UPDATE RESTRICT
```

## Составные Foreign Keys

```sql
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);
```

## Самоссылающиеся Foreign Keys

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    manager_id INT,
    FOREIGN KEY (manager_id) REFERENCES employees(id)
);
```

## Удаление Foreign Keys

```sql
ALTER TABLE orders
DROP CONSTRAINT fk_user;
```

## Преимущества

1. **Referential Integrity**: Обеспечивает целостность данных
2. **Качество данных**: Предотвращает появление "осиротевших" записей
3. **Каскадные действия**: Автоматическая очистка связанных данных
4. **Документация**: Делает связи явными

## Соображения

- Foreign keys добавляют накладные расходы на операции INSERT/UPDATE/DELETE
- Могут влиять на производительность больших таблиц
- Требуют индексов на столбцах foreign key (обычно создаются автоматически)
- Могут усложнять миграцию и очистку данных
