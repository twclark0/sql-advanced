```sql
create temporary table temp_users (user_handle uuid, first_name text, last_name text, email text);

copy temp_users(user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;

update users set email = temp_users.email from temp_users where users.user_handle = temp_users.user_handle;

drop table temp_users;

```
