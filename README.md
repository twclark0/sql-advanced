#### Setup your table

```sql
create table users (
user_handle uuid Primary key,
first_name text,
last_name text,
email text
);

```

```sql
create table purchases (
date date,
user_handle uuid,
sku uuid,
quantity int
);

```

```sql
create table members (
start_date date,
end_date date,
user_handle uuid,
first_name text,
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
insert into users (user_handle, first_name, last_name, email) values ('57bd72d4-7115-11e9-a923-1681be663d3e', 'Lucie', 'Jones', 'Lucie-Jones@gmail.com');
```

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

create temporary table users_temp (create_date date, user_handle uuid, first_name text, last_name text, email text);

insert into users_temp (create_date, user_handle, last_name, first_name) values (now(), uuid_generate_v4(), 'jones', 'michelle');

select create_date from users_temp u inner join (select now() as date) n on u.create_date = n.date;

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

### Lesson 7 - Paginate Results with Offset and Limit

- Returns portions of rows generated by a query

```sql
select * from users limit 1 offset 1;
```

- Offset rows are still calculated so large offsets can be inefficient
- Make sure a order by is used to get a predictable result

### Lesson 8 - Filter Grouped and Aggregated Data with Having

- Used most of the time with a `group by` clause
- `where` filters data BEFORE `group by`, `having` is filtering on groups of data

```sql
insert into purchases values ('2019-02-02', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 1);
insert into purchases values ('2019-02-03', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 2);
insert into purchases values ('2019-02-04', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 3);

insert into purchases values ('2019-04-20', 'cebfb6c1-7e85-44b1-8408-4084d38f87dd', uuid_generate_v4(), 3);

select user_handle, sum(quantity) as total from purchases where sum(quantity) > 5 group by user_handle;

select user_handle, sum(quantity) as total from purchases group by user_handle having sum(quantity) > 5;

```

### Lesson 9 - Shorthand Join ons with Using statement

- Instead of explicitly defining column names, if they are the same, you can just state it once within the clause

###### From

```sql
select * from users u inner join purchases p on u.user_handle = p.user_handle;
```

####### To

```sql
select * from users inner join purchases using(user_handle);
```

### Lesson 10 - Defining variables with Do / Declare

- `Do` is an anonymous code block (transient anonymous function, with no parameters, returning void)
- "Blocks" has two sections, declarations and body. Declarations is optional, where variables are declared.
- Keywords: `do`, `$$`, `declare`, `begin`, `end`, `end if`, `:=`
- Can create sub-blocks within another block

###### Basic example

```sql
do $$
declare
    handle uuid := '29587e31-b631-4b2a-a31f-9771a43db98a';
begin
    insert into members values (now(), null, handle, 'Stacy', 'freemon');
end $$;
```

###### Select into variable

```sql
do $$
declare
    handle uuid := '099ab71b-53ad-4fc6-823a-5f22aabc2d45';
    createDate date;
begin
    select create_date into createDate from users where user_handle = handle;
    insert into members values (createDate, null, handle, 'Stacy', 'freemon');
end $$;
```

###### If blocks

```sql
do $$
declare
    handle uuid := '17c1ed46-fb34-4c81-ae16-85324da7e1ec';
    createDate date;
begin
    select create_date into createDate from users where user_handle = handle;
    if (createDate is not null) then
        insert into members values (createDate, null, handle, 'Kaitlin', 'Johnson');
    end if;
end $$;
```

###### Functions around variables referencing another variable

```sql
do $$
declare
    handle uuid := '3ebf30fd-f524-4ec6-b864-9cb2ae57f5ed';
    someDate date := '2019-04-01';
    createDate date := least('2019-01-12', someDate);
begin
    insert into members values (createDate, null, handle, 'Jason', 'Anderson');
end $$;
```

### Lesson 11 - Conditional Returns with case - when - then - else - end

- Can be used wherever an expression is valid
- If no else then returns `NULL`
- Close `case` with `end`

```sql
select first_name,
    case when status is null then 'member' else status end
from users;
```

```sql
select first_name, status
from users
where case when status is not null then create_date > '2019-01-01' end;
```

### Lesson 12 - Handle nulls with coalesce

- Returns first of arguments that is not `Null`. `Null` is returned when all are `Null`.

```sql
select first_name, coalesce(status, 'member')
from users;
```

### Lesson 13 - Perform Multiple Steps in One with Transactions

- Keywords are `begin`, `commit`, `rollback`, `savepoint`, `start transaction`
- If any fail then all rollback by default

```sql
start transaction;
insert into purchases values ('2019-05-20', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 1);
update purchases set quantity = 5;
commit;
```

```sql
begin;
    insert into purchases values ('2019-10-10', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 1);
    savepoint insert_save_point;
    insert into purchases values ('2019-05-20', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 1);
    rollback to insert_save_point;
    update purchases set quantity = 8;
commit;
```

### Lesson 14 - SQL Pattern Matching & Regular Expressions

- Keywords: `like`, `ilike`, `not like`, `similar to`, `not similar to`, `%`, `_`
- `ilike` is similar to `like`, just case insensitive
- Succeeds only if its pattern matches the _entire string_. Most Regex will match on parts of a string
- An underscore matches any single character
- Percent matches any sequence of zero or more characters
- `Similar to` has more metacharacters support (`|`,`*`, `+`,`?`, `()`)

```sql
select * from users where first_name like 'tyler';
```

```sql
select * from users where first_name like '%t';
```

```sql
select * from users where first_name like '_y___';
```

```sql
select * from users where first_name like '_y%';
```

```sql
select * from users where first_name not like 'tyler';
```

```sql
select * from users where first_name similar to '(t|m)%';
```

```sql
select * from users where first_name similar to 'tyler+';
```

### Lesson 15 - SQL Aggregate Inline filter

- Enables filtering on aggregate functions

```sql
select count(*) as total, count(*) as filtered from users where first_name = 'tyler';
```

vs.

```sql
select count(*) as total, count(*) filter (where first_name = 'tyler') as filtered from users;
```
