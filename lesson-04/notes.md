## Defining Custom types in SQL

- `CREATE TYPE` creates a new data type to use in database
- Composite, Enumerated, Range, Base, & Array types

#### Syntax

`CREATE TYPE name AS ENUM ( [ 'label' [, ... ] ] )`

#### Exercise

```sql
create type user_status as enum ('member', 'instructor', 'developer');
```

```sql
alter table users add column status user_status;
```

```sql
update users set status = 'instructor' where first_name = 'tyler';
```
