## Query PLanner Explain Analyze

- Displays the execution plan, critical for performance tuning
- Shows sequential scans, index scans, and algorithms used in joins
- Most valuable of all is the estimated statement execution cost (total time is what matters)
- **Explain** just shows plan, **Analyze** actually runs query

### Examples:

- Seq scan

```sql
explain (format json)  select * from users;
```

- Index scan

```sql
explain (format json) select * from users where user_handle = 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11';
```

- Aggregate and seq scan

```sql
explain (format json) select sum(quantity) from purchases where date < now();
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

_Notes_

- https://explain.depesz.com/
- Not always the execution on larger vs. small tables
- Careful with analyze, actually performs query
