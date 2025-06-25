```shell-session
sqlmap -h
```

```shell-session
sqlmap -hh
```

```shell-session

sqlmap -u "http://www.example.com/vuln.php?id=1" --batch
```

Note: in this case, option '-u' is used to provide the target URL, while the switch '--batch' is used for skipping any required user-input, by automatically choosing using the default option.
