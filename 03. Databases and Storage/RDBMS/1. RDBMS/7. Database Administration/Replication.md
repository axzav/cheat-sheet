# Репликация (Replication)

Репликация — процесс копирования и поддержания объектов базы данных в нескольких базах данных для повышения доступности, производительности и восстановления после сбоев.

## Типы репликации (Replication Types)

### Master-Slave (Primary-Replica)

- Один master принимает записи
- Одна или несколько replicas получают обновления
- Replicas могут обслуживать read queries
- **Use Case**: Масштабирование чтения, резервное копирование, восстановление после сбоев

### Master-Master (Multi-Master)

- Несколько masters принимают записи
- Изменения реплицируются между всеми masters
- **Use Case**: Географическое распределение, высокая доступность

### Statement-Based Replication

- Репликация SQL statements
- Проще, но менее точно

### Row-Based Replication

- Репликация фактических изменений строк
- Более точно, лучше для сложных запросов

### Logical Replication

- Репликация на логическом уровне (таблицы, схемы)
- Более гибкая, может фильтровать/трансформировать данные

## Репликация PostgreSQL

### Streaming Replication (Physical)

**Конфигурация Master (postgresql.conf):**
```ini
wal_level = replica
max_wal_senders = 3
wal_keep_segments = 32  # или wal_keep_size в новых версиях
```

**Конфигурация Master (pg_hba.conf):**
```
host replication replica_user 192.168.1.0/24 md5
```

**Настройка Replica:**
```bash
# Создать базовое резервное копирование
pg_basebackup -h master_host -D /var/lib/postgresql/data -U replica_user -P -W

# Настроить recovery (recovery.conf или postgresql.conf)
primary_conninfo = 'host=master_host user=replica_user'
```

### Logical Replication

**Создать Publication (Master):**
```sql
CREATE PUBLICATION my_publication FOR TABLE table1, table2;
-- или для всех таблиц
CREATE PUBLICATION all_tables FOR ALL TABLES;
```

**Создать Subscription (Replica):**
```sql
CREATE SUBSCRIPTION my_subscription
CONNECTION 'host=master_host dbname=mydb user=replica_user'
PUBLICATION my_publication;
```

## Репликация MySQL

### Настройка Master-Slave

**Конфигурация Master (my.cnf):**
```ini
[mysqld]
server-id = 1
log-bin = mysql-bin
binlog-format = ROW
```

**Создать пользователя репликации:**
```sql
CREATE USER 'replica_user'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'replica_user'@'%';
FLUSH PRIVILEGES;
```

**Получить позицию Master:**
```sql
SHOW MASTER STATUS;
```

**Конфигурация Slave (my.cnf):**
```ini
[mysqld]
server-id = 2
relay-log = mysql-relay-bin
```

**Настроить Slave:**
```sql
CHANGE MASTER TO
  MASTER_HOST='master_host',
  MASTER_USER='replica_user',
  MASTER_PASSWORD='password',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=1234;

START SLAVE;
SHOW SLAVE STATUS\G
```

### MySQL Group Replication

Для multi-master репликации:
```sql
-- Настроить group replication
SET GLOBAL group_replication_group_name = "group_name";
SET GLOBAL group_replication_start_on_boot = ON;
START GROUP_REPLICATION;
```

## Топологии репликации (Replication Topologies)

### Простая Master-Slave

```
Master → Slave
```

### Master с несколькими Slaves

```
     → Slave1
Master → Slave2
     → Slave3
```

### Цепочка Master-Slave

```
Master → Slave1 → Slave2
```

### Multi-Master Ring

```
Master1 ↔ Master2 ↔ Master3
```

## Мониторинг репликации (Monitoring Replication)

### PostgreSQL

```sql
-- Проверить статус репликации
SELECT * FROM pg_stat_replication;

-- Проверить lag
SELECT client_addr, state, sync_state, 
       pg_wal_lsn_diff(pg_current_wal_lsn(), sent_lsn) AS lag_bytes
FROM pg_stat_replication;
```

### MySQL

```sql
-- Проверить статус slave
SHOW SLAVE STATUS\G

-- Проверить lag репликации
SELECT 
    TIMESTAMPDIFF(SECOND, Master_Log_File_Position, Read_Master_Log_Pos) AS lag_seconds
FROM information_schema.slave_status;
```

## Replication Lag

Replication lag — задержка между записью на master и её репликацией на slaves.

**Причины:**
- Сетевая задержка
- Высокая нагрузка на запись
- Медленная обработка на replica
- Большие транзакции

**Решения:**
- Оптимизировать запросы
- Увеличить пропускную способность сети
- Использовать более быстрое оборудование для replicas
- Настроить параметры репликации

## Failover

### Автоматический Failover

- Использовать инструменты типа Patroni, repmgr (PostgreSQL)
- Использовать MySQL Group Replication или MHA (MySQL)

### Ручной Failover

1. Остановить записи на старом master
2. Повысить replica до master
3. Обновить конфигурацию приложения
4. Начать записи на новом master

## Use Cases

1. **Масштабирование чтения**: Распределение read queries по replicas
2. **Высокая доступность**: Failover на replica при сбое master
3. **Восстановление после сбоев**: Replica в другом месте
4. **Резервное копирование**: Использовать replica для резервного копирования (без влияния на master)
5. **Отчетность**: Запускать тяжелые отчеты на replica
6. **Географическое распределение**: Replicas ближе к пользователям

## Best Practices

1. Мониторить replication lag
2. Регулярно тестировать процедуры failover
3. Использовать connection pooling для read replicas
4. Учитывать replication lag в логике приложения
5. Держать replicas в синхронизации с master
6. Документировать топологию репликации
7. Использовать подходящий тип репликации для use case
8. Планировать сетевые разделения
