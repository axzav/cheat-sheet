# Резервное копирование и восстановление (Backup and Recovery)

Стратегии резервного копирования и восстановления критически важны для защиты данных и обеспечения непрерывности бизнеса.

## Типы резервного копирования (Backup Types)

### Full Backup (Полное резервное копирование)

Полная копия всей базы данных.

### Incremental Backup (Инкрементальное резервное копирование)

Копирование только изменений с момента последнего резервного копирования.

### Differential Backup (Дифференциальное резервное копирование)

Копирование изменений с момента последнего полного резервного копирования.

### Logical Backup (Логическое резервное копирование)

Копирование данных в формате SQL (портативно, читаемо).

### Physical Backup (Физическое резервное копирование)

Копирование сырых файлов базы данных (быстрее, специфично для конкретной СУБД).

## Резервное копирование в PostgreSQL

### pg_dump (Logical Backup)

```bash
# Полное резервное копирование базы данных
pg_dump -U username -d database_name > backup.sql

# Конкретная схема
pg_dump -U username -d database_name -n schema_name > backup.sql

# Сжатое резервное копирование
pg_dump -U username -d database_name -F c -f backup.dump

# Custom format (позволяет выборочное восстановление)
pg_dump -U username -d database_name -F c -f backup.dump

# Только схема (без данных)
pg_dump -U username -d database_name -s > schema.sql

# Только данные (без схемы)
pg_dump -U username -d database_name -a > data.sql
```

### pg_dumpall (Все базы данных)

```bash
# Резервное копирование всех баз данных
pg_dumpall -U username > all_databases.sql
```

### Physical Backup (Файловая система)

```bash
# Требует остановки базы данных или режима backup
pg_basebackup -D /backup/location -Ft -z -P
```

### Point-in-Time Recovery (PITR)

Требует:
1. Базовое резервное копирование
2. Включенное архивирование WAL (Write-Ahead Log)

```bash
# Включить архивирование WAL в postgresql.conf
wal_level = replica
archive_mode = on
archive_command = 'cp %p /path/to/archive/%f'
```

## Резервное копирование в MySQL

### mysqldump (Logical Backup)

```bash
# Полное резервное копирование базы данных
mysqldump -u username -p database_name > backup.sql

# Все базы данных
mysqldump -u username -p --all-databases > all_databases.sql

# Конкретные таблицы
mysqldump -u username -p database_name table1 table2 > backup.sql

# Только схема
mysqldump -u username -p --no-data database_name > schema.sql

# Только данные
mysqldump -u username -p --no-create-info database_name > data.sql

# Сжатое
mysqldump -u username -p database_name | gzip > backup.sql.gz
```

### Physical Backup (MySQL)

```bash
# Использование mysqlbackup (MySQL Enterprise)
# Или копирование директории данных (требует остановки сервера)
```

### Binary Log Backup

```bash
# Включить binary logging
# Резервное копирование binary logs для point-in-time recovery
mysqlbinlog binlog.000001 > binlog.sql
```

## Операции восстановления (Restore Operations)

### Восстановление PostgreSQL

```bash
# Восстановление из SQL файла
psql -U username -d database_name < backup.sql

# Восстановление из custom format
pg_restore -U username -d database_name backup.dump

# Восстановление конкретной схемы
pg_restore -U username -d database_name -n schema_name backup.dump
```

### Восстановление MySQL

```bash
# Восстановление из SQL файла
mysql -u username -p database_name < backup.sql

# Восстановление всех баз данных
mysql -u username -p < all_databases.sql
```

## Стратегии резервного копирования (Backup Strategies)

### Ежедневное полное резервное копирование

- Простое, но требует много места
- Подходит для небольших баз данных

### Еженедельное полное + ежедневное инкрементальное

- Сбалансированный подход
- Распространено для средних баз данных

### Непрерывное архивирование (PostgreSQL)

- Базовое резервное копирование + архивирование WAL
- Позволяет point-in-time recovery
- Лучше всего для критических систем

## Сценарии восстановления (Recovery Scenarios)

### Полное восстановление базы данных

```bash
# Восстановить полное резервное копирование
psql -U username -d database_name < full_backup.sql
```

### Point-in-Time Recovery

```bash
# Восстановить базовое резервное копирование
pg_restore base_backup.dump

# Воспроизвести WAL файлы до конкретного времени
# Настроить recovery.conf или postgresql.conf
```

### Восстановление на уровне таблицы

```bash
# Восстановить конкретную таблицу из резервной копии
pg_restore -t table_name -d database_name backup.dump
```

## Best Practices резервного копирования

1. **Регулярное расписание**: Автоматизировать резервное копирование
2. **Тестирование восстановления**: Регулярно тестировать восстановление из резервных копий
3. **Хранение вне площадки**: Хранить резервные копии в другом месте
4. **Шифрование**: Шифровать файлы резервных копий
5. **Политика хранения**: Определить, как долго хранить резервные копии
6. **Мониторинг**: Мониторить успех/неудачу резервного копирования
7. **Документация**: Документировать процедуры резервного копирования и восстановления
8. **Контроль версий**: Хранить несколько версий резервных копий

## Инструменты резервного копирования

- **pg_dump/pg_restore**: Стандартные инструменты PostgreSQL
- **mysqldump**: Стандартный инструмент MySQL
- **pgBackRest**: Продвинутый инструмент резервного копирования PostgreSQL
- **Barman**: Менеджер резервного копирования и восстановления PostgreSQL
- **Percona XtraBackup**: Инструмент физического резервного копирования MySQL
