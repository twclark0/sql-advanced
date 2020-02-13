## CTEs in SQL

### CTEs (Common Table Expressions)

- CTE: Common Table Expression
- Created using `with` (thought of as defining temporary tables)
- Can use any CRUD statement within the `with` which can be within any CRUD query

#### Syntax

`with cte_name as ( [select, delete query] )`

### Exercises

```sql

with dates as (select now() as date)

select * from dates;
```

#### A member history table. Contains a record of every event of a member

1.  A Member's _current_ email (Email can change over the years)
2.  Their start_date of when they originally bought a license

```sql

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
