### Conditional Returns with case - when - then - else - end

- Can be used wherever an expression is valid
- If no else then returns `NULL`
- Close `case` with `end`

```sql
select first_name,
    case when status is null then 'member' else status end
from users;
```

```sql
select first_name, status
from users
where case when status is not null then create_date > '2019-01-01' end;
```
