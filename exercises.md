# Lesson 1 - Creating our tables

- Create a table named users with the following columns: user_handle as a uuid, first_name as text, last_name as text, and email as text
- Create a table named purchases with the following columns: date as a date, user_handle as a uuid, sku as a uuid, and quantity as an int
- Drop the tables and recreate them
  _Answers_

```sql
create table users (
user_handle uuid,
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
drop table users;

```

# Lesson 2 - Alter our tables

- Alter the users table to make the user_handle the primary key

_Answer_

```sql
alter table users add primary key (user_handle);
```

- Alter the purchases table to make an index on the user_handle

_Answer_

```sql
create index purchases_user_handle_index ON purchases (user_handle);
```

- Alter the users table and purchases table to make all columns be not null

_Answer_

```sql
alter table users alter first_name set not null;
```

- Drop both tables and recreate them with these constraints to begin with

_Answers_

```sql
create table users (
user_handle uuid Primary key,
first_name text not null,
last_name text not null,
email text not null
);
```

```sql
create table purchases (
date date not null,
user_handle uuid not null,
sku uuid not null,
quantity int not null
);

create index purchases_user_handle_index ON purchases (user_handle);

```

# Lesson 3 - Add data to our users table

- Bulk insert from an csv into our users table

_Answers_

```sql
\copy users(user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;
```

- Bulk insert from a csv into the purchases table

_Answers_

```sql
\copy purchases(date, user_handle, sku, quantity) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-purchases.csv' DELIMITER ',' CSV HEADER;
```

- Remove all data and re-add it

_Answers_

```sql
truncate table users;
```

# Lesson 4 - Aggregate data

- Find largest purchase amount in the purchases table

_Answer_

```sql
select max(quantity) from purchases;
```

- Find the user_handle and date of the largest purchase

```sql
select pu.user_handle,
    pu.date,
    p.max_quantity
from purchases pu
inner  join (select max(quantity) as max_quantity from purchases) p on pu.quantity = p.max_quantity;
```

# Lesson 5 - Filtering data

- Find all the orders that happened between 01-01-2020 and 01-10-2020

_Answer_

```sql
select * from purchases where date >= '01-01-2020' and date <= '01-10-2020';
```

- Find all the orders that have a quantity greater than 5

_Answer_

```sql
select * from purchases where quantity > 5;
```

- Find the user*handles of those that have \_collectively* purchased more than 5 items over time

```sql
select user_handle, sum(quantity) as total from purchases group by user_handle having sum(quantity) > 5;
```

# Lesson 6 - Joining

- Left outer join users on purchases
- Right outer join users on purchases
- Full outer join users on purchases
- Inner join users on purchases

_Answers_

```sql
select * from users left outer join purchases using(user_handle);
```

```sql
select * from users right outer join purchases using(user_handle);
```

```sql
select * from users full outer join purchases using(user_handle);
```

```sql
select * from users inner join purchases using(user_handle);
```

# Lesson 7 - Subqueries

- Using a subquery, return the first name and start_date of the earliest start date row in the members table

_Answer_

```sql
select start_date, first_name from members where start_date = (select min(start_date) from members);
```

- Return the first name from users table and the total of each individuals purchases

_Answer_

```sql
select u.first_name, t.total from users u inner join (select sum(quantity) as total, user_handle from purchases group by user_handle) t using(user_handle);
```

# Lesson 8 - On conflict do stuff

- Use the on conflict clause to try inserting Lucie into the users table

_Answer_

```sql
insert into users (user_handle, first_name, last_name, email) values ('57bd72d4-7115-11e9-a923-1681be663d3e', 'Lucie', 'Jones', 'Lucie-Jones@gmail.com') on conflict (user_handle) do nothing;
```

- Use the on conflict clause to try inserting Lucie into the users table. If there is a conflict then update her values with new data ('57bd72d4-7115-11e9-a923-1681be663d3e', 'Lucie', 'Jones', 'Lucie-Jones@gmail.com')

_Answer_

```sql
insert into users (user_handle, first_name, last_name, email) values ('57bd72d4-7115-11e9-a923-1681be663d3e', 'Lucie', 'Jones', 'Lucie-Jones@gmail.com') on conflict (user_handle) do update set first_name = excluded.first_name, last_name = excluded.last_name, email = excluded.email;

```

# Lesson 9 - CTEs

- Show a member's current email as well as the start_date of when they originally bought a license. So we need to get info from a user's earliest start date and latest start date.

_Answer_

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

- Use a CTE to delete rows from a table and then insert those deleted into a new table

_Answer_

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

# Lesson 10 - Do / Declare / Variables

- Using the do declare statement, create a username variable and reference it in an insert statement into the members table: (now(), null, \$handle, 'Stacy', 'freemon')

_Answer_

```sql
do $$
declare
    handle uuid := '29587e31-b631-4b2a-a31f-9771a43db98a';
begin
    insert into members values (now(), null, handle, 'Stacy', 'freemon');
end $$;

```

- Delete that row and do the same thing with one additonal step. Use a `select into` statement to assign Stacy's create_date to a variable that will be called in the insert statement. (\$createdate, null, \$handle, 'Stacy', 'freemon')

_Answer_

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

- Add on to the last excirse by only inserting this row if the create_date is not null. (You will use an if block around the createdate variable)

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
