## Bulk Insert / Update

##### Syntax:

From csv to postgres

`COPY table_name [ ( column_name [, ...] ) ] FROM { 'filename' | STDIN } [ [ WITH ] ( option [, ...] ) ]`

From postgres to csv

`COPY { table_name [ ( column_name [, ...] ) ] | ( query ) } TO { 'filename' | STDOUT } [ [ WITH ] ( option [, ...] ) ]`

Options:

    ```sql
    FORMAT format_name
    OIDS [ boolean ]
    DELIMITER 'delimiter_character'
    NULL 'null_string'
    HEADER [ boolean ]
    QUOTE 'quote_character'
    ESCAPE 'escape_character'
    FORCE_QUOTE { ( column_name [, ...] ) | * }
    FORCE_NOT_NULL ( column_name [, ...] )
    ENCODING 'encoding_name'
    ```

#### Insert

```sql
copy users (user_handle,first_name,last_name,email) FROM '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;
```

```sql
copy users (user_handle,first_name,last_name,email) to '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users-copy.csv' DELIMITER ',' CSV HEADER;

```
