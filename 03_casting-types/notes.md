## Casting Types in SQL

- Two types can be binary coercible, which means that the conversion can be performed "for free" without invoking any function.
- This requires that corresponding values use the same internal representation.

#### Syntax

`CAST (source_type AS target_type) WITH FUNCTION function_name (argument_type [, ...]) [ AS ASSIGNMENT | AS IMPLICIT ]`

#### Exercise

```sql
cast (expression as target_type)

```

```sql
select cast (now() as date);

select cast ('100' as integer);

```

- PostgreSQL only type cast :: operator

```sql
select now()::date;
```

```sql

create temporary table users_temp (create_date date, user_handle uuid, first_name text, last_name text, email text);

insert into users_temp (create_date, user_handle, last_name, first_name) values (now(), uuid_generate_v4(), 'jones', 'michelle');

select create_date from users_temp u inner join (select now() as date) n on u.create_date = n.date;

```
