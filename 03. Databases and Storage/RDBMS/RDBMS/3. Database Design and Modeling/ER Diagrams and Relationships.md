# ER Diagrams and Relationships

Entity-Relationship (ER) diagrams are visual representations of database structure showing entities, attributes, and relationships.

## Entity Types

- **Entity**: Object or concept (e.g., User, Order, Product)
- **Attribute**: Property of entity (e.g., name, email, price)
- **Relationship**: Association between entities

## Relationship Types

### One-to-One (1:1)
One entity relates to exactly one other entity.

**Example**: User ↔ UserProfile
- Each user has one profile
- Each profile belongs to one user

### One-to-Many (1:N)
One entity relates to many other entities.

**Example**: User → Orders
- One user can have many orders
- Each order belongs to one user

### Many-to-Many (M:N)
Many entities relate to many other entities.

**Example**: Students ↔ Courses
- One student can take many courses
- One course can have many students
- Requires junction/join table

## ER Diagram Notation

### Chen Notation
- Rectangles: Entities
- Diamonds: Relationships
- Ovals: Attributes
- Lines: Connections

### Crow's Foot Notation
- Rectangles: Entities
- Lines with symbols: Relationships
  - | (one)
  - O (zero or one)
  - < (many)

## Cardinality

Describes the number of relationships:
- **1**: Exactly one
- **0..1**: Zero or one (optional)
- **1..***: One or many
- **0..***: Zero or many (many, optional)
- **N**: Exactly N
- **M..N**: Between M and N

## Relationship Attributes

Relationships can have attributes:
- **Example**: Enrollment (Student ↔ Course)
  - Attribute: grade, enrollment_date

## Weak Entities

Entities that depend on another entity:
- Cannot exist without parent entity
- Partial key (depends on parent's key)

**Example**: OrderItem depends on Order

## Converting ER to Relational Model

### Entities → Tables
Each entity becomes a table with attributes as columns.

### Relationships → Foreign Keys
- 1:1: Foreign key in either table
- 1:N: Foreign key in "many" side
- M:N: Junction table with foreign keys to both entities

### Example
```
User (1) ──< (N) Order
```

Becomes:
```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## Design Principles

1. **Normalization**: Eliminate redundancy
2. **Clear Relationships**: Define relationships explicitly
3. **Appropriate Cardinality**: Model real-world accurately
4. **Naming Conventions**: Use clear, consistent names
5. **Documentation**: Document complex relationships

## Tools

- **Draw.io / diagrams.net**: Free diagramming
- **Lucidchart**: Online diagramming
- **dbdiagram.io**: Database-specific diagrams
- **pgAdmin**: PostgreSQL ER diagram tool
- **MySQL Workbench**: MySQL ER diagrams
- **dbForge**: Database design tools

## Common Patterns

### Hierarchical (Self-Referencing)
```
Employee (1) ──< (N) Employee
(manager-employee relationship)
```

### Junction Table Pattern
```
Student (N) ──< (N) Course
         ↓
    Enrollment
    (with attributes: grade, date)
```

### Audit Pattern
```
Main Table ──< (1) Audit Table
(tracks changes)
```
