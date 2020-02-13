## Query PLanner Explain Analyze

- Displays the execution plan, critical for performance tuning
- Shows sequential scans, index scans, and algorithms used in joins
- Most valuable of all is the estimated statement execution cost (total time is what matters)
- **Explain** just shows plan, **Analyze** actually runs query
- https://explain.depesz.com/

#### Syntax

`EXPLAIN [ ( option [, ...] ) ] statement`

`EXPLAIN [ ANALYZE ][ verbose ] statement`

- Option can be one of

  `ANALYZE [ boolean ]`
  `VERBOSE [ boolean ]`
  `COSTS [ boolean ]`
  `BUFFERS [ boolean ]`
  `FORMAT { TEXT | XML | JSON | YAML }`

#### Exercise

- Seq scan

```sql
explain select * from users;
```

- Index scan

```sql
explain select * from users where user_handle = 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11';
```

- Aggregate and seq scan

```sql
explain select sum(quantity) from purchases where date < now();
```

- Explain & Analyze

```sql
explain analyze select * from users where user_handle = 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11';
```

- Hash node shows hash buckets, batches, and peak amount of memory used for the hash table

```sql
explain analyze select * from users inner join purchases using(user_handle);
```

vs.

```sql
explain analyze select * from users inner join (select * from purchases) s using(user_handle);
```
