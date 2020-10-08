# Describe Table

If `psql` is available, use either `\d <table_name>` or `\d+ <table_name>`.
If not, you can query `information_schema.columns` like:

```sql
SELECT
    table_schema,
    table_name,
    column_name,
    data_type,
    is_nullable,
    column_default
FROM
    information_schema.columns
WHERE
    table_name = '<table_name>';
```

To make it more understandable at a glance which column is `NOT NULL`, you can use:

```sql
SELECT
    table_schema,
    table_name,
    column_name,
    data_type,
    CASE is_nullable WHEN 'YES' THEN '' ELSE 'not null' END AS is_nullable,
    column_default
FROM
    information_schema.columns
WHERE
    table_name = '<table_name>';
```

But `information_schema.columns` doesn't give you indexes, constraints, and tables that reference it.

You need to run separate queries for them:

## Indexes

```sql
SELECT
    schemaname,
    tablename,
    indexname,
    indexdef
FROM
    pg_indexes
WHERE
    tablename = '<table_name>';
```

## Constraints

### All constraints

```sql
SELECT
    pgccu.table_schema,
    pgccu.table_name,
    pgccu.column_name,
    CASE contype
      WHEN 'c' THEN 'check constraint'
      WHEN 'f' THEN 'foreign key constraint'
      WHEN 'p' THEN 'primary key constraint'
      WHEN 'u' THEN 'unique constraint'
      WHEN 't' THEN 'constraint trigger'
      WHEN 'x' THEN 'exclusion constraint'
    END AS constraint_type,
    pgc.conname AS constraint_name
FROM
    pg_constraint pgc
    JOIN pg_namespace pgn ON pgn.oid = pgc.connamespace
    LEFT JOIN information_schema.constraint_column_usage pgccu
        ON pgc.conname = pgccu.constraint_name
        AND pgn.nspname = pgccu.constraint_schema
WHERE
    pgccu.table_name = '<table_name>'
ORDER BY
    contype;
```

### Primary, foreign, and unique constraints

```sql
SELECT
    tc.table_schema,
    tc.table_name,
    kcu.column_name,
    tc.constraint_type,
    tc.constraint_name
FROM
    information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
            ON tc.constraint_name = kcu.constraint_name
            AND tc.table_schema = kcu.table_schema
WHERE
    tc.table_name = '<table_name>'
ORDER BY
    tc.constraint_type;
```

### Foreign key constraints with details

```sql
SELECT DISTINCT ON (tc.constraint_name)
    tc.constraint_name,
    tc.table_schema,
    tc.table_name,
    kcu.column_name,
    ccu.table_schema AS foreign_table_schema,
    ccu.table_name AS foreign_table_name,
    ccu.column_name AS foreign_column_name
FROM
    information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
            ON tc.constraint_name = kcu.constraint_name
            AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
            ON ccu.constraint_name = tc.constraint_name
            AND ccu.table_schema = tc.table_schema
WHERE
    tc.constraint_type = 'FOREIGN KEY' AND tc.table_name = '<table_name>';
```

### Check constraints with details

```sql
SELECT
    pgccu.table_schema,
    pgccu.table_name,
    pgccu.column_name,
    pgc.conname AS constraint_name,
    pgc.consrc AS definition
FROM
    pg_constraint pgc
    JOIN pg_namespace pgn ON pgn.oid = pgc.connamespace
    LEFT JOIN information_schema.constraint_column_usage pgccu
        ON pgc.conname = pgccu.constraint_name
        AND pgn.nspname = pgccu.constraint_schema
WHERE
    contype = 'c' AND pgccu.table_name = '<table_name>';
```

## Referenced by

To list tables that reference the table in question, you need to find foreign key constraints to the table in question:

```sql
SELECT DISTINCT ON (tc.constraint_name)
    tc.constraint_name,
    tc.table_schema,
    tc.table_name,
    kcu.column_name,
    ccu.table_schema AS foreign_table_schema,
    ccu.table_name AS foreign_table_name,
    ccu.column_name AS foreign_column_name
FROM
    information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
            ON tc.constraint_name = kcu.constraint_name
            AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
            ON ccu.constraint_name = tc.constraint_name
            AND ccu.table_schema = tc.table_schema
WHERE
    tc.constraint_type = 'FOREIGN KEY' AND ccu.table_name = '<table_name>';
```
