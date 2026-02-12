# Backup and Recovery

Backup and recovery strategies are critical for protecting data and ensuring business continuity.

## Backup Types

### Full Backup
Complete copy of the entire database.

### Incremental Backup
Only backs up changes since the last backup.

### Differential Backup
Backs up changes since the last full backup.

### Logical Backup
Backs up data in SQL format (portable, readable).

### Physical Backup
Backs up raw database files (faster, database-specific).

## PostgreSQL Backup

### pg_dump (Logical Backup)

```bash
# Full database backup
pg_dump -U username -d database_name > backup.sql

# Specific schema
pg_dump -U username -d database_name -n schema_name > backup.sql

# Compressed backup
pg_dump -U username -d database_name -F c -f backup.dump

# Custom format (allows selective restore)
pg_dump -U username -d database_name -F c -f backup.dump

# Only schema (no data)
pg_dump -U username -d database_name -s > schema.sql

# Only data (no schema)
pg_dump -U username -d database_name -a > data.sql
```

### pg_dumpall (All Databases)

```bash
# Backup all databases
pg_dumpall -U username > all_databases.sql
```

### Physical Backup (File System)

```bash
# Requires database to be stopped or in backup mode
pg_basebackup -D /backup/location -Ft -z -P
```

### Point-in-Time Recovery (PITR)

Requires:
1. Base backup
2. WAL (Write-Ahead Log) archiving enabled

```bash
# Enable WAL archiving in postgresql.conf
wal_level = replica
archive_mode = on
archive_command = 'cp %p /path/to/archive/%f'
```

## MySQL Backup

### mysqldump (Logical Backup)

```bash
# Full database backup
mysqldump -u username -p database_name > backup.sql

# All databases
mysqldump -u username -p --all-databases > all_databases.sql

# Specific tables
mysqldump -u username -p database_name table1 table2 > backup.sql

# Only schema
mysqldump -u username -p --no-data database_name > schema.sql

# Only data
mysqldump -u username -p --no-create-info database_name > data.sql

# Compressed
mysqldump -u username -p database_name | gzip > backup.sql.gz
```

### Physical Backup (MySQL)

```bash
# Using mysqlbackup (MySQL Enterprise)
# Or copy data directory (requires server shutdown)
```

### Binary Log Backup

```bash
# Enable binary logging
# Backup binary logs for point-in-time recovery
mysqlbinlog binlog.000001 > binlog.sql
```

## Restore Operations

### PostgreSQL Restore

```bash
# Restore from SQL file
psql -U username -d database_name < backup.sql

# Restore from custom format
pg_restore -U username -d database_name backup.dump

# Restore specific schema
pg_restore -U username -d database_name -n schema_name backup.dump
```

### MySQL Restore

```bash
# Restore from SQL file
mysql -u username -p database_name < backup.sql

# Restore all databases
mysql -u username -p < all_databases.sql
```

## Backup Strategies

### Daily Full Backup
- Simple but storage-intensive
- Good for small databases

### Weekly Full + Daily Incremental
- Balanced approach
- Common for medium databases

### Continuous Archiving (PostgreSQL)
- Base backup + WAL archiving
- Enables point-in-time recovery
- Best for critical systems

## Recovery Scenarios

### Complete Database Recovery
```bash
# Restore full backup
psql -U username -d database_name < full_backup.sql
```

### Point-in-Time Recovery
```bash
# Restore base backup
pg_restore base_backup.dump

# Replay WAL files up to specific time
# Configure recovery.conf or postgresql.conf
```

### Table-Level Recovery
```bash
# Restore specific table from backup
pg_restore -t table_name -d database_name backup.dump
```

## Backup Best Practices

1. **Regular Schedule**: Automate backups
2. **Test Restores**: Regularly test backup restoration
3. **Offsite Storage**: Store backups in different location
4. **Encryption**: Encrypt backup files
5. **Retention Policy**: Define how long to keep backups
6. **Monitoring**: Monitor backup success/failure
7. **Documentation**: Document backup and recovery procedures
8. **Version Control**: Keep multiple backup versions

## Backup Tools

- **pg_dump/pg_restore**: PostgreSQL standard tools
- **mysqldump**: MySQL standard tool
- **pgBackRest**: Advanced PostgreSQL backup tool
- **Barman**: PostgreSQL backup and recovery manager
- **Percona XtraBackup**: MySQL physical backup tool
- **mysqldump**: MySQL logical backup tool
