# Show Granted Privileges

You can find the details of PostgreSQL privileges [here](https://www.postgresql.org/docs/12/ddl-priv.html).

## Show table-level privileges

You can run `\dp` (or `\dp <table_name>`) with `psql` or run:

```sql
SELECT table_catalog, table_schema, table_name, privilege_type
FROM   information_schema.table_privileges
WHERE  grantee = 'USER_NAME';
```

ref: https://stackoverflow.com/questions/26917508/check-postgres-access-for-a-user/26917620

Replace `USER_NAME` with the actual user you'd like to check the permissions of.

When you do `\dp`, you'll see something like `calvin=r*w/hobbes`.

This means,

> the role calvin has the privilege `SELECT (r)` with grant `option (*)` as well as the non-grantable privilege `UPDATE (w)`, both granted by the role hobbes.

You can learn the details of them by reading the document you can find the link at the top of this document.

## Show schema-level privileges

If you'd like to see schema-level permissions roughly, run `\dn+` or use the query below:

```sql
SELECT table_catalog, table_schema, privilege_type
FROM   information_schema.table_privileges
WHERE  grantee = 'USER_NAME'
GROUP BY table_catalog, table_schema, privilege_type;
```

## Show default privileges

Type `\ddp` with `psql` or run the following query:

```sql
SELECT
  nspname,         -- schema name
  defaclobjtype,   -- object type
  defaclacl        -- default access privileges
FROM pg_default_acl a JOIN pg_namespace b ON a.defaclnamespace=b.oid;
```

ref: https://stackoverflow.com/questions/14555062/display-default-access-privileges-for-relations-sequences-and-functions-in-post
