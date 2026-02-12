# Replication

Replication is the process of copying and maintaining database objects in multiple databases to improve availability, performance, and disaster recovery.

## Replication Types

### Master-Slave (Primary-Replica)
- One master accepts writes
- One or more replicas receive updates
- Replicas can serve read queries
- **Use Case**: Read scaling, backup, disaster recovery

### Master-Master (Multi-Master)
- Multiple masters accept writes
- Changes replicated between all masters
- **Use Case**: Geographic distribution, high availability

### Statement-Based Replication
- Replicates SQL statements
- Simpler but less precise

### Row-Based Replication
- Replicates actual row changes
- More precise, better for complex queries

### Logical Replication
- Replicates at logical level (tables, schemas)
- More flexible, can filter/transform data

## PostgreSQL Replication

### Streaming Replication (Physical)

**Master Configuration (postgresql.conf):**
```ini
wal_level = replica
max_wal_senders = 3
wal_keep_segments = 32  # or wal_keep_size in newer versions
```

**Master Configuration (pg_hba.conf):**
```
host replication replica_user 192.168.1.0/24 md5
```

**Replica Setup:**
```bash
# Take base backup
pg_basebackup -h master_host -D /var/lib/postgresql/data -U replica_user -P -W

# Configure recovery (recovery.conf or postgresql.conf)
primary_conninfo = 'host=master_host user=replica_user'
```

### Logical Replication

**Create Publication (Master):**
```sql
CREATE PUBLICATION my_publication FOR TABLE table1, table2;
-- or for all tables
CREATE PUBLICATION all_tables FOR ALL TABLES;
```

**Create Subscription (Replica):**
```sql
CREATE SUBSCRIPTION my_subscription
CONNECTION 'host=master_host dbname=mydb user=replica_user'
PUBLICATION my_publication;
```

## MySQL Replication

### Master-Slave Setup

**Master Configuration (my.cnf):**
```ini
[mysqld]
server-id = 1
log-bin = mysql-bin
binlog-format = ROW
```

**Create Replication User:**
```sql
CREATE USER 'replica_user'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'replica_user'@'%';
FLUSH PRIVILEGES;
```

**Get Master Position:**
```sql
SHOW MASTER STATUS;
```

**Slave Configuration (my.cnf):**
```ini
[mysqld]
server-id = 2
relay-log = mysql-relay-bin
```

**Configure Slave:**
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

For multi-master replication:
```sql
-- Configure group replication
SET GLOBAL group_replication_group_name = "group_name";
SET GLOBAL group_replication_start_on_boot = ON;
START GROUP_REPLICATION;
```

## Replication Topologies

### Simple Master-Slave
```
Master → Slave
```

### Master with Multiple Slaves
```
     → Slave1
Master → Slave2
     → Slave3
```

### Master-Slave Chain
```
Master → Slave1 → Slave2
```

### Multi-Master Ring
```
Master1 ↔ Master2 ↔ Master3
```

## Monitoring Replication

### PostgreSQL
```sql
-- Check replication status
SELECT * FROM pg_stat_replication;

-- Check lag
SELECT client_addr, state, sync_state, 
       pg_wal_lsn_diff(pg_current_wal_lsn(), sent_lsn) AS lag_bytes
FROM pg_stat_replication;
```

### MySQL
```sql
-- Check slave status
SHOW SLAVE STATUS\G

-- Check replication lag
SELECT 
    TIMESTAMPDIFF(SECOND, Master_Log_File_Position, Read_Master_Log_Pos) AS lag_seconds
FROM information_schema.slave_status;
```

## Replication Lag

Replication lag is the delay between a write on the master and its replication to slaves.

**Causes:**
- Network latency
- Heavy write load
- Slow replica processing
- Large transactions

**Solutions:**
- Optimize queries
- Increase network bandwidth
- Use faster hardware for replicas
- Tune replication settings

## Failover

### Automatic Failover
- Use tools like Patroni, repmgr (PostgreSQL)
- Use MySQL Group Replication or MHA (MySQL)

### Manual Failover
1. Stop writes to old master
2. Promote replica to master
3. Update application configuration
4. Start writes to new master

## Use Cases

1. **Read Scaling**: Distribute read queries across replicas
2. **High Availability**: Failover to replica if master fails
3. **Disaster Recovery**: Replica in different location
4. **Backup**: Use replica for backups (no master impact)
5. **Reporting**: Run heavy reports on replica
6. **Geographic Distribution**: Replicas closer to users

## Best Practices

1. Monitor replication lag
2. Test failover procedures regularly
3. Use connection pooling for read replicas
4. Consider replication lag in application logic
5. Keep replicas in sync with master
6. Document replication topology
7. Use appropriate replication type for use case
8. Plan for network partitions
