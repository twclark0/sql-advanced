### Filter Grouped and Aggregated Data with Having

- Used most of the time with a `group by` clause
- `where` filters data BEFORE `group by`, `having` is filtering on groups of data

#### Syntax

`having` then aggregate

#### Exercise

```sql
insert into purchases values ('2019-02-02', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 1);
insert into purchases values ('2019-02-03', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 2);
insert into purchases values ('2019-02-04', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 3);

insert into purchases values ('2019-04-20', 'cebfb6c1-7e85-44b1-8408-4084d38f87dd', uuid_generate_v4(), 3);

select user_handle, sum(quantity) as total from purchases where sum(quantity) > 5 group by user_handle;

select user_handle, sum(quantity) as total from purchases group by user_handle having sum(quantity) > 5;

```
