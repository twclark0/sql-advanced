## Exercise (Updating with the copy command and temp tables)

1. Create a temp table named `temp_users`
2. Bulk insert `sql-advanced-users-exercise.csv` into the temp table
3. `update` `users` table email column with the values from the temp table `temp_users`
4. Drop the temp table `temp_users`

#### Insights

- temp tables only exist for the duration of a database session.
- Can use `temp` or `temporary`

#### You know you did it right when `Lucie`'s email is:

- `Lucie-Smith@gmail.com` and not `Lucie-Clark@gmail.com` within the `users` table
