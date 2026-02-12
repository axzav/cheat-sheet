# Security and Access Control

Database security involves managing users, roles, permissions, and access control to protect data and ensure proper authorization.

## User Management

### Creating Users

#### PostgreSQL
```sql
CREATE USER username WITH PASSWORD 'password';
-- or
CREATE ROLE username WITH LOGIN PASSWORD 'password';
```

#### MySQL
```sql
CREATE USER 'username'@'hostname' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';  -- any host
```

### Modifying Users

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

### Dropping Users

```sql
DROP USER username;              -- PostgreSQL
DROP USER 'username'@'hostname'; -- MySQL
```

## Role Management

### PostgreSQL Roles

```sql
-- Create role
CREATE ROLE role_name;

-- Grant role to user
GRANT role_name TO username;

-- Revoke role
REVOKE role_name FROM username;
```

### MySQL Roles (8.0+)

```sql
-- Create role
CREATE ROLE 'role_name';

-- Grant role to user
GRANT 'role_name' TO 'username'@'hostname';

-- Set default role
SET DEFAULT ROLE 'role_name' TO 'username'@'hostname';
```

## Permissions (GRANT/REVOKE)

### Granting Permissions

```sql
-- Grant SELECT on table
GRANT SELECT ON table_name TO username;

-- Grant multiple permissions
GRANT SELECT, INSERT, UPDATE ON table_name TO username;

-- Grant all permissions
GRANT ALL PRIVILEGES ON table_name TO username;

-- Grant on database
GRANT ALL PRIVILEGES ON DATABASE db_name TO username;

-- Grant on schema (PostgreSQL)
GRANT ALL PRIVILEGES ON SCHEMA schema_name TO username;
```

### Revoking Permissions

```sql
REVOKE SELECT ON table_name FROM username;
REVOKE ALL PRIVILEGES ON table_name FROM username;
```

## Common Privileges

- **SELECT**: Read data
- **INSERT**: Add new rows
- **UPDATE**: Modify existing rows
- **DELETE**: Remove rows
- **CREATE**: Create new objects
- **DROP**: Delete objects
- **ALTER**: Modify object structure
- **INDEX**: Create indexes
- **REFERENCES**: Create foreign keys
- **TRIGGER**: Create triggers
- **EXECUTE**: Execute functions/procedures

## Row-Level Security (PostgreSQL)

```sql
-- Enable RLS on table
ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;

-- Create policy
CREATE POLICY policy_name ON table_name
    FOR SELECT
    TO username
    USING (user_id = current_user_id());
```

## Viewing Permissions

### PostgreSQL
```sql
-- List roles
\du

-- List permissions
\dp table_name
```

### MySQL
```sql
-- Show grants for user
SHOW GRANTS FOR 'username'@'hostname';

-- Show current user grants
SHOW GRANTS;
```

## Security Best Practices

1. **Principle of Least Privilege**: Grant minimum necessary permissions
2. **Strong Passwords**: Enforce password policies
3. **Regular Audits**: Review user permissions periodically
4. **Separate Accounts**: Use different accounts for different purposes
5. **Encryption**: Encrypt data at rest and in transit
6. **Network Security**: Restrict database access by IP/host
7. **Audit Logging**: Enable audit logs for security events
8. **Backup Security**: Secure backup files

## Connection Security

### PostgreSQL (pg_hba.conf)
```
# Allow local connections with password
host    all    all    127.0.0.1/32    md5

# Allow specific IP
host    all    all    192.168.1.0/24  md5
```

### MySQL (bind-address)
```ini
# In my.cnf
bind-address = 127.0.0.1  # Only local connections
# or
bind-address = 0.0.0.0    # All interfaces
```
