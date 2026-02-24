---
name: MySQL Patterns
description: Provides MySQL data type selection, indexing strategy, query optimization, and anti-pattern avoidance
---

# MySQL Patterns

## Data Types

Select data types with explicit intent. Never rely on implicit defaults.

| Use Case | Type | Rationale |
|---|---|---|
| Primary keys | `BIGINT UNSIGNED AUTO_INCREMENT` | Avoids overflow for large tables |
| Short strings (name, email) | `VARCHAR(255)` | Explicit max length, storage-efficient |
| Long text (bio, description) | `TEXT` | No length limit in query, stored off-page |
| Timestamps | `DATETIME(3)` | Millisecond precision, no timezone conversion issues |
| Money / decimals | `DECIMAL(19,4)` | Exact precision, no floating-point rounding |
| Booleans | `TINYINT(1)` | MySQL convention (Prisma maps Boolean to this) |
| Structured data | `JSON` | Queryable with JSON functions, validated on write |
| UUIDs | `CHAR(36)` or `BINARY(16)` | Use `BINARY(16)` for performance-critical tables |

## Character Set

Always set `utf8mb4` as the character set and `utf8mb4_unicode_ci` as the collation. The legacy `utf8` charset in MySQL only supports 3-byte characters and silently truncates emojis and some CJK characters.

```sql
CREATE TABLE users (
  id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) NOT NULL,
  created_at DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## Index Strategy

### B-tree Indexes (Default)

Use for equality (`=`), range (`>`, `<`, `BETWEEN`), prefix matching (`LIKE 'abc%'`), and `ORDER BY` optimization.

### Composite Index Column Order

Place columns in this order:
1. Equality conditions first (`WHERE status = 'active'`)
2. Range conditions last (`WHERE created_at > '2024-01-01'`)

A composite index on `(status, created_at)` supports both `WHERE status = 'active'` and `WHERE status = 'active' AND created_at > '2024-01-01'`, but not `WHERE created_at > '2024-01-01'` alone.

### FULLTEXT Indexes

Use for natural language text search on `VARCHAR` or `TEXT` columns.

```sql
ALTER TABLE posts ADD FULLTEXT INDEX ft_posts_title_body (title, body);
SELECT * FROM posts WHERE MATCH(title, body) AGAINST('search term' IN BOOLEAN MODE);
```

### Covering Indexes

Include all columns referenced in SELECT, WHERE, and ORDER BY to serve the query entirely from the index without table lookups.

```sql
-- If the query is: SELECT id, status FROM orders WHERE user_id = ? ORDER BY created_at DESC
CREATE INDEX idx_orders_covering ON orders (user_id, created_at DESC, id, status);
```

## Query Optimization

### Use EXPLAIN ANALYZE

Run `EXPLAIN ANALYZE` on every slow query (over 100ms) to inspect the actual execution plan.

```sql
EXPLAIN ANALYZE
SELECT u.name, COUNT(o.id) as order_count
FROM users u
JOIN orders o ON o.user_id = u.id
WHERE u.status = 'active'
GROUP BY u.id;
```

### Optimization Rules

- Select only needed columns. Never use `SELECT *` in application queries.
- Place the smaller result set table on the driving side of JOINs.
- Use `EXISTS` instead of `IN` for subqueries against large tables.
- Add `LIMIT` to queries that do not need the full result set.

## InnoDB Specifics

- **Row-level locking**: InnoDB locks individual rows during transactions, not entire tables. This allows concurrent writes to different rows.
- **Foreign key support**: InnoDB enforces referential integrity. Define foreign keys in the schema and let the engine reject invalid references.
- **ACID compliance**: All transactions are atomic, consistent, isolated, and durable by default.
- **Clustered index**: The primary key is the clustered index. Rows are physically stored in primary key order. Choose sequential keys (AUTO_INCREMENT) to avoid page splits.

## Pagination

### Cursor-Based (Use This)

Use a cursor (typically the last seen `id`) for pagination on large datasets. This approach has constant performance regardless of page depth.

```sql
-- First page
SELECT id, title, created_at FROM posts ORDER BY id DESC LIMIT 20;

-- Next page (cursor = last id from previous page)
SELECT id, title, created_at FROM posts WHERE id < ? ORDER BY id DESC LIMIT 20;
```

### OFFSET-Based (Avoid for Large Datasets)

`OFFSET` forces MySQL to scan and discard rows. At `OFFSET 100000`, the database reads 100,020 rows and discards 100,000. Use cursor-based pagination instead.

## Anti-Patterns

| Anti-Pattern | Problem | Correct Approach |
|---|---|---|
| `SELECT * FROM ...` | Fetches unused columns, breaks covering indexes | Select only needed columns |
| `OFFSET` for deep pages | O(N) scan cost | Cursor-based pagination |
| `WHERE YEAR(created_at) = 2024` | Function on column prevents index use | `WHERE created_at >= '2024-01-01' AND created_at < '2025-01-01'` |
| `ENUM` columns | Altering values requires ALTER TABLE | Use a lookup table with foreign key |
| Implicit type conversion | `WHERE phone = 12345` on VARCHAR column skips index | Match types: `WHERE phone = '12345'` |
| Missing index on foreign key | Slow JOINs and cascading deletes | Add index on every foreign key column |

## Example

**Unoptimized query** (full table scan, no index used):

```sql
SELECT * FROM orders
WHERE YEAR(created_at) = 2024
  AND status = 'shipped'
ORDER BY created_at DESC
OFFSET 5000 LIMIT 20;
```

Problems: `SELECT *`, function on indexed column, OFFSET on deep page.

**Optimized query** with proper index:

```sql
-- Create composite index (equality first, range second)
CREATE INDEX idx_orders_status_created ON orders (status, created_at DESC);

-- Cursor-based query selecting only needed columns
SELECT id, user_id, total, created_at
FROM orders
WHERE status = 'shipped'
  AND created_at >= '2024-01-01'
  AND created_at < '2025-01-01'
  AND id < ?
ORDER BY created_at DESC
LIMIT 20;
```

Result: Index range scan, no table lookup for covered columns, constant pagination cost.
