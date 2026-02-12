# SQL (Structured Query Language)

**SQL** - язык SQL является стандартом для реляционных баз данных. Этот язык предоставляет возможности для работы с данными в базе, работы со схемой данных и администрирования базы

SQL состоит из 4х основных компонентов:

- **Data Definition Language (DDL)**. Это возможности языка для создания таблиц, изменения таблиц, создания ключей и тд. Включает такие выражения как CREATE, ALTER, DROP и другие
- **Data Manipulation Language (DML)**. Это выражения для работы с данными в базе - SELECT, INSERT, UPDATE, and DELETE
- **Data Control Language (DCL)**. Используется для ограничения доступа к данным в базе - GRANT and REVOKE
- **Transaction Control Language (TCL)**. Работа с транзакциями - COMMIT, ROLLBACK, and SAVEPOINT

## Data Definition Language (DDL)

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);
```

## Data Manipulation Language (DML)

```sql
INSERT INTO users (id, username, email, password)
VALUES (1, 'john_doe', 'john@example.com', 'securepassword');
```

## Data Control Language (DCL)

//todo

## Transaction Control Language (TCL)

// todo


**TODO** - версии SQL, основные фичи версий и какие DBMS какой стандарт используют?