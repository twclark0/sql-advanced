### Defining variables with Do / Declare

- `Do` is an anonymous code block (transient anonymous function, with no parameters, returning void)
- "Blocks" has two sections, declarations and body. Declarations is optional, where variables are declared.
- Keywords: `do`, `$$`, `declare`, `begin`, `end`, `end if`, `:=`
- Can create sub-blocks within another block

###### Basic example

```sql
do $$
declare
    handle uuid := '29587e31-b631-4b2a-a31f-9771a43db98a';
begin
    insert into members values (now(), null, handle, 'Stacy', 'freemon');
end $$;
```

###### Select into variable

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

###### If blocks

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

###### Functions around variables referencing another variable

```sql
do $$
declare
    handle uuid := '3ebf30fd-f524-4ec6-b864-9cb2ae57f5ed';
    someDate date := '2019-04-01';
    createDate date := least('2019-01-12', someDate);
begin
    insert into members values (createDate, null, handle, 'Jason', 'Anderson');
end $$;
```
