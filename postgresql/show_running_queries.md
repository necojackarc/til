# Show Running Queries

```sql
SELECT pid, age(clock_timestamp(), query_start), usename, query 
FROM pg_stat_activity
WHERE query != '<IDLE>' AND query NOT ILIKE '%pg_stat_activity%' 
ORDER BY query_start desc;
```

[This gist](https://gist.github.com/rgreenjr/3637525) is a good reference to learn this kind of queries.
