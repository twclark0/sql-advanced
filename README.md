#### Setup your table

```sql
create table users (
user_handle uuid Primary key,
first_name text,
last_name text,
email text
);

```

## Lesson 1 Bulk Insert / Update

#### Insert

```sql
\copy users(user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;
```

#### Update

```sql
create temporary table users_temp (user_handle uuid, first_name text, last_name text, email text);

\copy users_temp(user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;

update users set email = users_temp.email from users_temp where users.user_handle = users_temp.user_handle;

drop table users_temp;

```

- Notes: temp tables only exist for the duration of a database session.
- Can use `temp` or `temporary`
- `DROP TABLE temp_table_name;`

## Lesson 2 On Conflicts Do Stuff

```sql
select * from users where first_name = 'Lucie';
```

then do a insert if not found or an update with the new information.

```sql
insert into users (user_handle, first_name, last_name, email) values ('57bd72d4-7115-11e9-a923-1681be663d3e', 'Lucie', 'Jones', 'Lucie-Jones@gmail.com') on conflict do nothing;

```

```sql
insert into users (user_handle, first_name, last_name, email) values ('57bd72d4-7115-11e9-a923-1681be663d3e', 'Lucie', 'Jones', 'Lucie-Jones@gmail.com') on conflict (user_handle) do update set first_name = excluded.first_name, last_name = excluded.last_name, email = excluded.email;

```

###### Notes

- Only available from PostgreSQL 9.5
- target can be column name, `on constraint constraint_name` (like the unique constraint), or with a where predicate
- action can be `do nothing` or `do update set`

## Lesson 3 Casting Types in SQL

- Using the cast

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
insert into users (create_date, user_handle, last_name, first_name) values (now(), uuid_generate_v4(), 'jones', 'michelle');

select create_date from users u inner join (select now() as date) n on u.create_date = n.date;

```

## Lesson 4 Defining Custom types in SQL

- `CREATE TYPE` creates a new data type to use in database
- Composite, Enumerated, Range, Base, & Array types

```sql
create type user_status as enum ('member', 'instructor', 'developer');
```

```sql
alter table users add column status user_status;
```

```sql
update users set status = 'instructor' where first_name = 'tyler';
```

## Lesson 5 Query PLanner Explain Analyze

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

## Lesson 6 CTEs in SQL

### CTEs

- CTE: Common Table Expression
- Created using `with` (thought of as defining temporary tables)
- Can use any CRUD statement within the `with` which can be within any CRUD query

##### Basic implementation

```sql

with dates as (select now() as date)

select * from dates;
```

##### Selecting real world

- Here's the problem: Show a member's current email as well as the start_date of when they originally bought a license. So we need to get info from a user's earliest start date and latest start date.

```sql
create table members (
    start_date date,
    end_date date,
    user_handle uuid,
    first_name text,
    email text
);

insert into members values ('2018-01-01',  '2019-01-01', '57bd8cd8-7115-11e9-a923-1681be663d3e', 'tyler', 'orignal@gmail.com');
insert into members values ('2019-02-20', null, '57bd8cd8-7115-11e9-a923-1681be663d3e', 'joe', 'new@gmail.com');

with most_recent_membership as (
    select distinct on (user_handle) user_handle, email
        from members
    order by user_handle, start_date desc
)

select mr.email, min(m.start_date) from members m
left outer join most_recent_membership mr using(user_handle) group by mr.email;

```

##### Deleting

```sql
create table purhases_copy (
    date date,
    user_handle uuid,
    sku uuid,
    quantity int
);

with moved_purchases as (
    delete from purchases
    RETURNING *
)
insert into purhases_copy select * from moved_purchases;
```
