### Perform Multiple Steps in One with Transactions

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
