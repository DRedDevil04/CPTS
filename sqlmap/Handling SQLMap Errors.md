
## Display Errors
The first step is usually to switch the `--parse-errors`, to parse the DBMS errors (if any) and displays them as part of the program run:

## Store the Traffic

The `-t` option stores the whole traffic content to an output file:

```shell-session
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt

```

## Verbose Output

Another useful flag is the `-v` option, which raises the verbosity level of the console output:

```shell-session
sqlmap -u "http://www.target.com/vuln.php?id=1" -v 6 --batch
```

Finally, we can utilize the `--proxy` option to redirect the whole traffic through a (MiTM) proxy (e.g., `Burp`). This will route all SQLMap traffic through `Burp`, so that we can later manually investigate all requests, repeat them, and utilize all features of `Burp` with these requests

