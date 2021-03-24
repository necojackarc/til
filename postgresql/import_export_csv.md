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

## Import CSV while creating a table

If you'd like to import a CSV file into a new table, you can use [pgfutter](https://github.com/lukasmartinelli/pgfutter).
This will create a new table with the header names defined in the CSV. It looks like all columns are created as TEXT columns.

```bash
$ pgfutter --db <db_name> --host <host_name> --user <user_name> --pw <password> --schema <schema_name> --table <table_name> csv -d ',' <csv_file_to_import>
```
