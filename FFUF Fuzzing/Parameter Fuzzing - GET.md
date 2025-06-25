
## GET Request Fuzzing

- `http://admin.academy.htb:PORT/admin/admin.php?param1=key`.

 `/opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt`


```shell-session

ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx
```
