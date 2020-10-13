# Import/Export CSV

## Export CSV

```sql
\COPY (SELECT * FROM <table_name>) TO 'output.csv' WITH CSV HEADER
```

## Import CSV

```sql
\COPY <table_name> FROM 'path/to/file.csv' DELIMITER ',' CSV HEADER
```


```sql
\COPY <table_name>(<column_name1, column_name2>) FROM 'path/to/file.csv' DELIMITER ',' CSV HEADER
```
