## Bulk Insert / Update

- temp tables only exist for the duration of a database session.
- Can use `temp` or `temporary`
- `DROP TABLE temp_table_name;`

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
