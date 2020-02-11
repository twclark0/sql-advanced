## Bulk Insert / Update

##### Syntax:

From csv to postgres

`COPY table_name [ ( column_name [, ...] ) ] FROM { 'filename' | STDIN } [ [ WITH ] ( option [, ...] ) ]`

From postgres to csv

`COPY { table_name [ ( column_name [, ...] ) ] | ( query ) } TO { 'filename' | STDOUT } [ [ WITH ] ( option [, ...] ) ]`

Options:

    `FORMAT format_name
    OIDS [ boolean ]
    DELIMITER 'delimiter_character'
    NULL 'null_string'
    HEADER [ boolean ]
    QUOTE 'quote_character'
    ESCAPE 'escape_character'
    FORCE_QUOTE { ( column_name [, ...] ) | * }
    FORCE_NOT_NULL ( column_name [, ...] )
    ENCODING 'encoding_name'
    `

#### Exercise:

#### Insert

```sql
copy users(user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;
```

#### Update

```sql
create temporary table users_temp (user_handle uuid, first_name text, last_name text, email text);

copy users_temp(user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;

update users set email = users_temp.email from users_temp where users.user_handle = users_temp.user_handle;

drop table users_temp;

```

- temp tables only exist for the duration of a database session.
- Can use `temp` or `temporary`
- `DROP TABLE temp_table_name;`
