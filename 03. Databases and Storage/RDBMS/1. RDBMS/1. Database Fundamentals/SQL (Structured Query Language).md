# SQL (Structured Query Language)

**SQL** (Structured Query Language) — стандартизированный язык для работы с реляционными базами данных. Предоставляет возможности для работы с данными, управления схемой данных и администрирования базы.

SQL состоит из 4 основных компонентов:

- **Data Definition Language (DDL)** — команды для создания и изменения структуры базы данных: таблиц, индексов, ключей и других объектов. Основные команды: `CREATE`, `ALTER`, `DROP`, `TRUNCATE`
- **Data Manipulation Language (DML)** — команды для работы с данными в таблицах: `SELECT`, `INSERT`, `UPDATE`, `DELETE`
- **Data Control Language (DCL)** — команды для управления доступом к данным и объектам базы данных: `GRANT`, `REVOKE`
- **Transaction Control Language (TCL)** — команды для управления транзакциями: `COMMIT`, `ROLLBACK`, `SAVEPOINT`

## Data Definition Language (DDL)

DDL используется для создания, изменения и удаления структуры базы данных.

### Создание таблицы

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Изменение таблицы

```sql
ALTER TABLE users 
ADD COLUMN last_login TIMESTAMP;

ALTER TABLE users 
ALTER COLUMN email SET NOT NULL;

ALTER TABLE users 
DROP COLUMN password;
```

### Удаление таблицы

```sql
DROP TABLE users;
```

## Data Manipulation Language (DML)

DML используется для работы с данными в таблицах.

### Вставка данных

```sql
INSERT INTO users (id, username, email, password)
VALUES (1, 'john_doe', 'john@example.com', 'securepassword');
```

### Обновление данных

```sql
UPDATE users 
SET email = 'newemail@example.com' 
WHERE id = 1;
```

### Удаление данных

```sql
DELETE FROM users 
WHERE id = 1;
```

## Data Control Language (DCL)

DCL используется для управления правами доступа к объектам базы данных.

### Предоставление прав

```sql
-- Предоставить права на выборку данных
GRANT SELECT ON users TO user_role;

-- Предоставить все права на таблицу
GRANT ALL PRIVILEGES ON users TO admin_role;

-- Предоставить права на выполнение функций
GRANT EXECUTE ON FUNCTION calculate_total TO user_role;
```

### Отзыв прав

```sql
-- Отозвать права на выборку
REVOKE SELECT ON users FROM user_role;

-- Отозвать все права
REVOKE ALL PRIVILEGES ON users FROM admin_role;
```

## Transaction Control Language (TCL)

TCL используется для управления транзакциями и обеспечения целостности данных.

### Начало транзакции

```sql
BEGIN;  -- или START TRANSACTION;
```

### Подтверждение изменений

```sql
COMMIT;
```

### Откат изменений

```sql
ROLLBACK;
```

### Точки сохранения

```sql
BEGIN;
-- выполнение операций
SAVEPOINT sp1;
-- выполнение дополнительных операций
ROLLBACK TO SAVEPOINT sp1;  -- откат к точке сохранения
COMMIT;
```