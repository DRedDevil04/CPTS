Tip: There shouldn't be any spaces between '-p' and the password.

we added another SQL query after a semi-colon (;). Though this is actually not possible with MySQL, it is possible with MSSQL and PostgreSQL

### Union Clause

`UNION` combined the output of both `SELECT` statements into one

A `UNION` statement can only operate on `SELECT` statements with an equal number of columns.

## Un-even Columns

 can put junk data for the remaining required columns so that the total number of columns we are `UNION`ing with remains the same as the original query.

 When filling other columns with junk data, we must ensure that the data type matches the columns data type, otherwise the query will return an error. For the sake of simplicity, we will use numbers as our junk data, which will also become handy for tracking our payloads positions, as we will discuss later.

For advanced SQL injection, we may want to simply use 'NULL' to fill other columns, as 'NULL' fits all data types.




