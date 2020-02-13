### Conditional Returns with case - when - then - else - end

- Can be used wherever an expression is valid
- If no else then returns `NULL`
- Close `case` with `end`

#### Syntax

`CASE WHEN condition THEN result [WHEN ...] [ELSE result] END`

#### Exercise

```sql
select first_name,
    case when status is null then 'member' else status end
from users;
```

```sql
select first_name, start_date
from members
where case when email is not null then start_date > '2019-01-01' end;
```
