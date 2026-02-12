# Naming Conventions

## Общие принципы

### 1. Консистентность превыше всего

Выбери один стиль и придерживайся его во всей базе данных. Смешение стилей хуже, чем выбор "неправильного" стиля.

### 2. Избегай зарезервированных слов

Не используй SQL keywords (`user`, `order`, `group`, `table`, `select`, и т.д.) в качестве имен таблиц или колонок. Если необходимо — используй кавычки, но лучше выбрать альтернативное имя.

### 3. Без сокращений (кроме общепринятых)

❌ Плохо: `usr` (user), `cfg` (config), `dt` (date), `tbl` (table)
✅ Хорошо: `id`, `url`, `ip`, `uuid`

### 4. Без префиксов для типов объектов

❌ Плохо: `tbl_user`, `tbl_order`, `fld_name`, `col_email`
✅ Хорошо: `user`, `order`, `name`, `email`

**Исключение:** Префиксы допустимы для:
- Views: `v_active_users`, `mv_monthly_sales`
- Триггеров: `trg_user_set_updated_at`
- Constraints: `fk_`, `idx_`, `pk_`, `uniq_`

### 5. Без версионирования в именах

❌ Плохо: `user_v2`, `order_new`, `product_final`

---

## Стили именования

### Snake Case (lower_snake_case) — **рекомендуется**

Использует подчеркивания для разделения слов, все буквы в нижнем регистре.

**Примеры**: `user`, `order_item`, `user_id`, `created_at`, `is_active`

**Плюсы:**
- ✅ Универсален для всех СУБД
- ✅ Улучшает читаемость (особенно для людей с проблемами зрения)
- ✅ Снижает неоднозначность (например, `under_value` vs `undervalue`)
- ✅ В PostgreSQL автоматически приводится к lowercase
- ✅ Не требует кавычек

### Camel Case (camelCase)

Первая буква в нижнем регистре, остальные слова с заглавной.

**Примеры:**
- `user`
- `orderItem`
- `userId`
- `createdAt`
- `isActive`

**Плюсы:**
- ✅ Короче, чем snake_case
- ✅ Популярен в некоторых экосистемах (Java, JavaScript)

**Минусы:**
- ❌ В PostgreSQL требует кавычек или автоматически приводится к lowercase
- ❌ Может создавать неоднозначность (`Undervalue` vs `UnderValue` в case-insensitive SQL)
- ❌ Сложнее читать для неанглоязычных разработчиков

### Pascal Case (PascalCase)

Все слова с заглавной буквы.

**Примеры:**
- `User`
- `OrderItem`
- `UserId`
- `CreatedAt`
- `IsActive`

**Плюсы:**
- ✅ Используется в Microsoft SQL Server (AdventureWorks)
- ✅ Популярен в .NET экосистеме

**Минусы:**
- ❌ В PostgreSQL требует кавычек
- ❌ Несовместим с автоматическим lowercase в PostgreSQL
- ❌ Может конфликтовать с зарезервированными словами

### Рекомендация

**Используйте `lower_snake_case`** для всех объектов базы данных. Это де-факто стандарт для PostgreSQL и MySQL, обеспечивает максимальную совместимость и читаемость.

---

## Таблицы

### Singular vs Plural

#### Singular (единственное число) — **рекомендуется многими**

**Примеры:**
- `user`
- `order`
- `payment`
- `invoice_item`

**Аргументы за:**
- ✅ Строка таблицы представляет одну сущность
- ✅ Проще читать JOIN'ы: `FROM user JOIN order ON order.user_id = user.id`
- ✅ Соответствует концептуальной модели (таблица = коллекция сущностей, строка = одна сущность)
- ✅ Используется в Rails, Laravel и многих современных фреймворках

**Аргументы против:**
- ⚠️ Может конфликтовать с зарезервированными словами (`user`, `order`)

#### Plural (множественное число)

**Примеры:**
- `users`
- `orders`
- `payments`

**Аргументы за:**
- ✅ Меньше конфликтов с зарезервированными словами
- ✅ Интуитивно понятно, что таблица содержит множество записей
- ✅ Используется в некоторых enterprise системах

**Аргументы против:**
- ⚠️ Менее семантически точное (строка = одна сущность, не множество)

#### Рекомендация

**Используйте singular** для основных таблиц. Это более современный подход и лучше отражает семантику. Для избежания конфликтов с зарезервированными словами используйте более специфичные имена (`customer_order` вместо `order`).

### Join-таблицы (many-to-many)

**Формат:** `<entity1>_<entity2>` (в алфавитном порядке)

**Примеры:**
- `user_role`
- `order_product`
- `post_tag`

**Важно:** Соблюдайте алфавитный порядок для консистентности:
- ✅ `post_tag` (не `tag_post`)
- ✅ `user_role` (не `role_user`)

### Не повторяйте контекст

❌ Плохо:
- `user_table`
- `order_entity`
- `product_data`

✅ Хорошо:
- `user`
- `order`
- `product`

---

## Колонки

### Primary Key

#### Подход 1: Просто `id` (в таблице `user`)

**Пример:**
```sql
CREATE TABLE user (
    id BIGINT PRIMARY KEY,
    ...
);
```

**Плюсы:**
- ✅ Коротко и понятно
- ✅ Стандарт для многих ORM
- ✅ В контексте таблицы недвусмысленно

**Минусы:**
- ❌ В сложных JOIN'ах может быть неочевидно, какой `id` используется
- ❌ Может привести к ошибкам при сравнении разных типов ID

#### Подход 2: Полное имя `<table>_id` (рекомендуется)

**Пример:**
```sql
CREATE TABLE user (
    user_id BIGINT PRIMARY KEY,
    ...
);
```

**Плюсы:**
- ✅ Явно указывает тип ID в любом контексте
- ✅ Помогает находить ошибки в JOIN'ах на этапе написания
- ✅ Упрощает рефакторинг и отладку

**Минусы:**
- ⚠️ Длиннее

**Пример проблемы с простым `id`:**

```sql
-- ❌ Ошибка не очевидна
SELECT thread.*
FROM email thread
JOIN email selected ON selected.id = thread.id
JOIN star origin ON origin.id = thread.id  -- ошибка: star.id != email.id
JOIN station st ON st.id = origin.id       -- ошибка: station.id != star.id
```

```sql
-- ✅ Ошибка видна сразу
SELECT thread.*
FROM email thread
JOIN email selected ON selected.email_id = thread.email_id
JOIN star origin ON origin.star_id = thread.email_id  -- очевидно: ошибка!
JOIN station st ON st.station_id = origin.star_id      -- очевидно: ошибка!
```

#### Рекомендация

**Используйте полное имя `<table>_id`** для primary key. Это особенно важно в больших проектах с множеством JOIN'ов.

### Foreign Key

**Формат:** `<referenced_table>_id`

**Примеры:**
- `user_id` → ссылается на `user.id`
- `order_id` → ссылается на `order.id`
- `parent_id` → ссылается на `parent.id` (self-reference)

**Важно:** Имя foreign key должно совпадать с именем primary key в целевой таблице, если возможно.

**Пример:**
```sql
-- ✅ Хорошо: имена совпадают
CREATE TABLE order (
    order_id BIGINT PRIMARY KEY,
    user_id BIGINT REFERENCES user(user_id)
);

-- ❌ Плохо: имена не совпадают
CREATE TABLE order (
    order_id BIGINT PRIMARY KEY,
    owner BIGINT REFERENCES user(user_id)  -- неочевидно, что это FK
);
```

**Исключение:** Если нужна семантика, используйте описательное имя:
- `owning_company_id` вместо просто `company_id` (если важно подчеркнуть владение)

### Boolean поля

**Формат:** `is_<attribute>`, `has_<attribute>`, `can_<action>`

**Примеры:**
- `is_active`
- `is_deleted`
- `has_children`
- `can_edit`
- `is_published`

❌ Плохо:
- `active` (теряется семантика boolean)
- `deleted` (неясно, что это boolean)

### Даты и время

**Формат:** `<event>_at` для timestamp, `<event>_date` для date

**Примеры:**
- `created_at`
- `updated_at`
- `deleted_at`
- `published_at`
- `birth_date`
- `expiry_date`

❌ Плохо:
- `create_date`
- `dt_created`
- `upd_time`
- `created`

### Денежные поля

**Указывайте единицы измерения или валюту:**

**Примеры:**
- `amount` (если контекст ясен)
- `amount_usd`
- `price_cents` (для целых чисел в центах/копейках)
- `price` (если используется decimal с фиксированной точностью)

**Рекомендация:** Используйте целые числа для денег (копейки/центы), а не float:
- ✅ `price_cents INT` (100 = $1.00)
- ❌ `price FLOAT` (проблемы с точностью)

### Enum-like поля

**Используйте простые имена:**

✅ Хорошо:
- `status` (значения: `'pending'`, `'approved'`, `'rejected'`)
- `type` (значения: `'premium'`, `'basic'`)
- `role` (значения: `'admin'`, `'user'`)

❌ Плохо:
- `order_status_type` (если таблица уже `order`)
- `user_role_type` (избыточность)

### Избегайте неоднозначности

**Указывайте единицы измерения и контекст:**

❌ Плохо:
```sql
SELECT name, 'too cold'
FROM areas
WHERE temperature < 32;  -- 32 что? Фаренгейт? Цельсий?
```

✅ Хорошо:
```sql
SELECT name, 'too cold'
FROM areas
WHERE fahrenheit < 32;  -- явно указана единица измерения
```

### Не повторяйте контекст таблицы

❌ Плохо (в таблице `order`):
- `order_id`
- `order_date`
- `order_total`

✅ Хорошо:
- `id` или `order_id` (для PK)
- `date`
- `total`

---

## Constraints

### Primary Key Constraint

**Формат:** `pk_<table>`

**Пример:**
```sql
ALTER TABLE user
ADD CONSTRAINT pk_user PRIMARY KEY (user_id);
```

### Foreign Key Constraint

**Формат:** `fk_<table>_<column>_<referenced_table>`

**Пример:**
```sql
ALTER TABLE order
ADD CONSTRAINT fk_order_user_id_user
FOREIGN KEY (user_id) REFERENCES user(user_id);
```

### Unique Constraint

**Формат:** `uq_<table>_<column>` или `uniq_<table>_<column>`

**Пример:**
```sql
ALTER TABLE user
ADD CONSTRAINT uq_user_email UNIQUE (email);
```

### Check Constraint

**Формат:** `chk_<table>_<description>`

**Пример:**
```sql
ALTER TABLE user
ADD CONSTRAINT chk_user_age_positive CHECK (age >= 0);

ALTER TABLE order
ADD CONSTRAINT chk_order_amount_non_negative CHECK (amount >= 0);
```

---

## Индексы

### Обычный индекс

**Формат:** `idx_<table>_<column>`

**Примеры:**
- `idx_user_email`
- `idx_order_created_at`
- `idx_product_category_id`

### Составной индекс

**Формат:** `idx_<table>_<col1>_<col2>_...`

**Пример:**
- `idx_order_user_id_created_at`
- `idx_user_email_status`

### Уникальный индекс

**Формат:** `uniq_<table>_<column>`

**Пример:**
- `uniq_user_email`
- `uniq_product_sku`

**Примечание:** В PostgreSQL можно использовать expression indexes:

```sql
CREATE INDEX idx_user_email_lower
ON user (lower(email));
```

---

## Views

**Формат:** `v_<description>`

**Примеры:**
- `v_active_users`
- `v_monthly_sales`
- `v_user_orders_summary`

**Materialized views:**
- `mv_monthly_sales`
- `mv_daily_statistics`

---

## Триггеры

**Формат:** `trg_<table>_<event>_<action>`

**Примеры:**
- `trg_user_set_updated_at` (устанавливает `updated_at` при обновлении)
- `trg_order_calculate_total` (пересчитывает total при изменении items)
- `trg_audit_log_insert` (логирует вставки)

---

## Sequences (PostgreSQL)

**Формат:** `<column>_seq`

**Пример:**
- `user_id_seq`
- `order_id_seq`

---

## Различия между СУБД

### PostgreSQL

**Особенности:**
- Автоматически приводит некавыченные идентификаторы к **lowercase**
- Поддерживает **schemas** (namespaces): `auth.user`, `billing.invoice`
- Имеет отдельные **sequence** объекты
- Поддерживает **expression indexes** и **partial indexes**
- Более строгая модель имен

**Рекомендации:**
- ✅ Используйте только `lower_snake_case`
- ✅ Избегайте кавычек
- ✅ Используйте schemas для организации (если нужно)
- ✅ Явно именуйте все constraints

**Пример проблемы:**
```sql
-- ❌ Создастся как 'useraccount' (все lowercase)
CREATE TABLE UserAccount (...);

-- ❌ Теперь нужно всегда использовать кавычки
CREATE TABLE "UserAccount" (...);
SELECT * FROM "UserAccount";  -- обязательно кавычки!

-- ✅ Правильно
CREATE TABLE user_account (...);
SELECT * FROM user_account;  -- без кавычек
```

### MySQL

**Особенности:**
- Поведение регистра зависит от `lower_case_table_names`:
  - Windows: case-insensitive
  - Linux: может быть case-sensitive
- Нет schemas (только databases)
- Нет отдельных sequences (используется `AUTO_INCREMENT`)
- Ограничения на длину имен индексов (64 символа)

**Рекомендации:**
- ✅ Используйте только `lower_snake_case`
- ✅ Избегайте CamelCase
- ✅ Учитывайте ограничения длины имен
- ✅ Явно именуйте constraints

**Пример проблемы:**
```sql
-- На Windows: работает
CREATE TABLE UserAccount (...);

-- На Linux: может не работать (зависит от настроек)
-- Лучше всегда использовать lowercase
CREATE TABLE user_account (...);
```

### SQL Server

**Особенности:**
- Популярен **PascalCase** (AdventureWorks sample)
- Использует **schemas** для организации
- Case-insensitive по умолчанию

**Рекомендации:**
- Можно использовать PascalCase, если команда предпочитает
- Или использовать snake_case для совместимости

---

## Антипаттерны

### ❌ Смешение стилей

```sql
-- Плохо
CREATE TABLE userProfile (...);
CREATE TABLE order_item (...);
CREATE TABLE OrderItem (...);
```

### ❌ Аббревиатуры

```sql
-- Плохо
CREATE TABLE usr_prf_cfg (...);
CREATE TABLE ord_itm (...);
```

### ❌ Бизнес-логика в именах

```sql
-- Плохо
CREATE TABLE active_user (...);
CREATE TABLE paid_order (...);

-- Хорошо
CREATE TABLE user (
    is_active BOOLEAN,
    ...
);

CREATE TABLE order (
    paid_at TIMESTAMP,
    ...
);
```

### ❌ Префиксы для типов объектов

```sql
-- Плохо
CREATE TABLE tbl_user (...);
CREATE TABLE t_order (...);

-- Хорошо
CREATE TABLE user (...);
CREATE TABLE order (...);
```

### ❌ Версионирование в именах

```sql
-- Плохо
CREATE TABLE user_v2 (...);
CREATE TABLE order_new (...);

-- Хорошо: используйте миграции
-- ALTER TABLE user ADD COLUMN ...;
```

---

## Практический минимальный стандарт

- ✅ **snake_case** для всех объектов
- ✅ **singular** для таблиц
- ✅ **`<table>_id`** для primary key
- ✅ **`<table>_id`** для foreign key
- ✅ **`created_at`**, **`updated_at`** для timestamps
- ✅ **`is_<attribute>`** для boolean
- ✅ Префиксы для constraints: **`pk_`**, **`fk_`**, **`idx_`**, **`uniq_`**, **`chk_`**
- ✅ Префиксы для views: **`v_`**, **`mv_`**
- ✅ Префиксы для триггеров: **`trg_`**

## Дополнительные рекомендации

### 1. Документируйте соглашения

Создайте документ с naming conventions для команды. Это особенно важно:
- При онбординге новых разработчиков
- При работе с несколькими проектами
- Для долгосрочной поддержки

### 2. Используйте линтеры и валидаторы

Настройте автоматическую проверку naming conventions:
- SQL linters
- Миграционные инструменты с валидацией
- Pre-commit hooks

### 3. Явно именуйте все constraints

Не полагайтесь на автогенерацию ORM:

```sql
-- ❌ Плохо: ORM сгенерирует что-то вроде FK_87hfg734hgfds
ALTER TABLE order ADD FOREIGN KEY (user_id) REFERENCES user(id);

-- ✅ Хорошо: явное имя
ALTER TABLE order
ADD CONSTRAINT fk_order_user_id_user
FOREIGN KEY (user_id) REFERENCES user(user_id);
```

### 4. Делайте имена grep-friendly

Имена должны легко находиться в коде:
- ✅ `user_id` — легко найти все использования
- ❌ `uid` — может совпадать с другими сокращениями

### 5. Учитывайте длину имен

Некоторые СУБД имеют ограничения:
- MySQL: 64 символа для индексов
- PostgreSQL: 63 символа для идентификаторов

Планируйте имена с учетом этих ограничений.

---

## Примеры хорошей схемы

```sql
-- Таблицы
CREATE TABLE user (
    user_id BIGINT PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    name VARCHAR(255),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE order (
    order_id BIGINT PRIMARY KEY,
    user_id BIGINT NOT NULL,
    total_cents INT NOT NULL,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Constraints
ALTER TABLE order
ADD CONSTRAINT fk_order_user_id_user
FOREIGN KEY (user_id) REFERENCES user(user_id);

ALTER TABLE user
ADD CONSTRAINT uq_user_email UNIQUE (email);

ALTER TABLE order
ADD CONSTRAINT chk_order_total_non_negative CHECK (total_cents >= 0);

-- Индексы
CREATE INDEX idx_user_email ON user(email);
CREATE INDEX idx_order_user_id_created_at ON order(user_id, created_at);
CREATE INDEX idx_order_status ON order(status);

-- Join-таблица
CREATE TABLE order_product (
    order_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    price_cents INT NOT NULL,
    PRIMARY KEY (order_id, product_id)
);

ALTER TABLE order_product
ADD CONSTRAINT fk_order_product_order_id_order
FOREIGN KEY (order_id) REFERENCES order(order_id);

ALTER TABLE order_product
ADD CONSTRAINT fk_order_product_product_id_product
FOREIGN KEY (product_id) REFERENCES product(product_id);
```
