# Lesson 1 - Creating our tables

- Create a table named users with the following columns: user_handle as a uuid, first_name as text, last_name as text, and email as text
- Create a table named purchases with the following columns: date as a date, user_handle as a uuid, sku as a uuid, and quantity as an int


*Answers*

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

# Lesson 2 - Alter our tables

- Alter the users table to make the user_handle the primary key

*Answer*

```sql
alter table users add primary key (user_handle);
```

- Alter the purchases table to make an index on the user_handle

*Answer*

```sql
create index purchases_user_handle_index ON purchases (user_handle);
```

- Alter the users table and purchases table to make all columns be not null

*Answer*

```sql
alter table users alter first_name set not null;
```

- Drop both tables and recreate them with these constraints to begin with

*Answers*

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

*Answers*

```sql
\copy users(user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;
```

- Bulk insert from a csv into the purchases table

*Answers*

```sql
\copy purchases(date, user_handle, sku, quantity) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-purchases.csv' DELIMITER ',' CSV HEADER;
```

# Lesson 4 - Aggregate data

- Find largest purchase amount in the purchases table

*Answer*

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

*Answer*
```sql
select * from purchases where date >= '01-01-2020' and date <= '01-10-2020';
```

- Find all the orders that have a quantity greater than 5

*Answer*
```sql
select * from purchases where quantity > 5;
```

- Find the user_handles of those that have *collectively* purchased more than 5 items over time

```sql
select user_handle, sum(quantity) as total from purchases group by user_handle having sum(quantity) > 5;
```