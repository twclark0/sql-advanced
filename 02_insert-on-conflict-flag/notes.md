## Conflicts Do Stuff

#### Notes

- Only available from PostgreSQL 9.5
- target can be column name, `on constraint constraint_name` (like the unique constraint), or with a where predicate
- action can be `do nothing` or `do update set`

#### Syntax

`INSERT INTO table_name [ AS alias ] [ ( column_name [, ...] ) ] ... [ ON CONFLICT [ conflict_target ] conflict_action ] ...`

- Where conflict_target can be one of:

`( { index_column_name | ( index_expression ) } [ COLLATE collation ] [ opclass ] [, ...] ) [ WHERE index_predicate ] ON CONSTRAINT constraint_name`

- conflict_action is one of:

`DO NOTHING DO UPDATE SET { column_name = { expression | DEFAULT } | ( column_name [, ...] ) = ( { expression | DEFAULT } [, ...] ) | ( column_name [, ...] ) = ( sub-SELECT ) } [, ...] [ WHERE condition ]`

#### Exercise

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
