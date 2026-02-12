# Безопасность и контроль доступа (Security and Access Control)

Безопасность базы данных включает управление пользователями, ролями, правами доступа и контроль доступа для защиты данных и обеспечения правильной авторизации.

## Управление пользователями (User Management)

### Создание пользователей

#### PostgreSQL

```sql
CREATE USER username WITH PASSWORD 'password';
-- или
CREATE ROLE username WITH LOGIN PASSWORD 'password';
```

#### MySQL

```sql
CREATE USER 'username'@'hostname' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';  -- любой хост
```

### Изменение пользователей

#### PostgreSQL

```sql
ALTER USER username WITH PASSWORD 'new_password';
ALTER USER username WITH SUPERUSER;
ALTER USER username WITH CREATEDB;
```

#### MySQL

```sql
ALTER USER 'username'@'hostname' IDENTIFIED BY 'new_password';
```

### Удаление пользователей

```sql
DROP USER username;              -- PostgreSQL
DROP USER 'username'@'hostname'; -- MySQL
```

## Управление ролями (Role Management)

### Роли PostgreSQL

```sql
-- Создать роль
CREATE ROLE role_name;

-- Назначить роль пользователю
GRANT role_name TO username;

-- Отозвать роль
REVOKE role_name FROM username;
```

### Роли MySQL (8.0+)

```sql
-- Создать роль
CREATE ROLE 'role_name';

-- Назначить роль пользователю
GRANT 'role_name' TO 'username'@'hostname';

-- Установить роль по умолчанию
SET DEFAULT ROLE 'role_name' TO 'username'@'hostname';
```

## Права доступа (GRANT/REVOKE)

### Предоставление прав

```sql
-- Предоставить SELECT на таблицу
GRANT SELECT ON table_name TO username;

-- Предоставить несколько прав
GRANT SELECT, INSERT, UPDATE ON table_name TO username;

-- Предоставить все права
GRANT ALL PRIVILEGES ON table_name TO username;

-- Предоставить права на базу данных
GRANT ALL PRIVILEGES ON DATABASE db_name TO username;

-- Предоставить права на схему (PostgreSQL)
GRANT ALL PRIVILEGES ON SCHEMA schema_name TO username;
```

### Отзыв прав

```sql
REVOKE SELECT ON table_name FROM username;
REVOKE ALL PRIVILEGES ON table_name FROM username;
```

## Общие привилегии (Common Privileges)

- **SELECT**: Чтение данных
- **INSERT**: Добавление новых строк
- **UPDATE**: Изменение существующих строк
- **DELETE**: Удаление строк
- **CREATE**: Создание новых объектов
- **DROP**: Удаление объектов
- **ALTER**: Изменение структуры объекта
- **INDEX**: Создание индексов
- **REFERENCES**: Создание внешних ключей
- **TRIGGER**: Создание триггеров
- **EXECUTE**: Выполнение функций/процедур

## Row-Level Security (PostgreSQL)

```sql
-- Включить RLS на таблице
ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;

-- Создать политику
CREATE POLICY policy_name ON table_name
    FOR SELECT
    TO username
    USING (user_id = current_user_id());
```

## Просмотр прав доступа

### PostgreSQL

```sql
-- Список ролей
\du

-- Список прав
\dp table_name
```

### MySQL

```sql
-- Показать права пользователя
SHOW GRANTS FOR 'username'@'hostname';

-- Показать права текущего пользователя
SHOW GRANTS;
```

## Best Practices безопасности

1. **Принцип наименьших привилегий**: Предоставлять минимально необходимые права
2. **Надежные пароли**: Применять политики паролей
3. **Регулярные аудиты**: Периодически проверять права пользователей
4. **Раздельные аккаунты**: Использовать разные аккаунты для разных целей
5. **Шифрование**: Шифровать данные в покое и при передаче
6. **Сетевая безопасность**: Ограничивать доступ к базе данных по IP/хосту
7. **Аудит логирования**: Включать audit logs для событий безопасности
8. **Безопасность резервных копий**: Защищать файлы резервных копий

## Безопасность подключений (Connection Security)

### PostgreSQL (pg_hba.conf)

```
# Разрешить локальные подключения с паролем
host    all    all    127.0.0.1/32    md5

# Разрешить конкретный IP
host    all    all    192.168.1.0/24  md5
```

### MySQL (bind-address)

```ini
# В my.cnf
bind-address = 127.0.0.1  # Только локальные подключения
# или
bind-address = 0.0.0.0    # Все интерфейсы
```
