
|**`Files`**|**`History`**|**`Memory`**|**`Key-Rings`**|
|---|---|---|---|
|Configs|Logs|Cache|Browser stored credentials|
|Databases|Command-line History|In-memory Processing||
|Notes||||
|Scripts||||
|Source codes||||
|Cronjobs||||
|SSH Keys|||

## Files

|   |   |   |
|---|---|---|
|Configuration files|Databases|Notes|
|Scripts|Cronjobs|SSH keys|
recompiling a service, the required filename for the basic configuration can be changed, which would result in the same effect

#### Configuration Files

```shell-session

for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

-v means exclude following patterns
`\|` is or operator in grep

#### Credentials in Configuration Files

In this example, we search for three words (`user`, `password`, `pass`) in each file with the file extension `.cnf`

```shell-session

for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done
```

#### Databases

```shell-session

for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
```


#### Notes

Files with no or .txt extension

```shell-session

find /home/* -type f -name "*.txt" -o ! -name "*.*"
```

-o OR operator
! not operator

#### Scripts

```shell-session

for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done
```

#### Cronjobs

Cronjobs are independent execution of commands, programs, scripts. These are divided into the system-wide area (`/etc/crontab`) and user-dependent executions. Some applications and scripts require credentials to run and are therefore incorrectly entered in the cronjobs. Furthermore, there are the areas that are divided into different time ranges (`/etc/cron.daily`, `/etc/cron.hourly`, `/etc/cron.monthly`, `/etc/cron.weekly`). The scripts and files used by `cron` can also be found in `/etc/cron.d/` for Debian-based distributions.

```shell-session
cat /etc/crontab
```

```shell-session
ls -la /etc/cron.*/
```

#### SSH Keys

#### SSH Private Keys

```shell-session
 grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"
```

#### SSH Public Keys

```shell-session
grep -rnw "ssh-rsa" /home/* 2>/dev/null | grep ":1"
```

`-r`: Recursively search through directories.
`-n`: Display line numbers in the output.
`-w`: Match the whole word "PRIVATE KEY".

**`| grep ":1"`**:

- Filters the output of the first `grep` command to include only lines where the match is on line 1 (indicated by `:1`).

## History

- .bash_history
- .bashrc
- .bash_profile

#### Bash History

```shell-session
tail -n5 /home/*/.bash*
```

#### Logs

|**Application Logs**|**Event Logs**|**Service Logs**|**System Logs**|
|---|---|---|---|

| **Log File**          | **Description**                                    |
| --------------------- | -------------------------------------------------- |
| `/var/log/messages`   | Generic system activity logs.                      |
| `/var/log/syslog`     | Generic system activity logs.                      |
| `/var/log/auth.log`   | (Debian) All authentication related logs.          |
| `/var/log/secure`     | (RedHat/CentOS) All authentication related logs.   |
| `/var/log/boot.log`   | Booting information.                               |
| `/var/log/dmesg`      | Hardware and drivers related information and logs. |
| `/var/log/kern.log`   | Kernel related warnings, errors and logs.          |
| `/var/log/faillog`    | Failed login attempts.                             |
| `/var/log/cron`       | Information related to cron jobs.                  |
| `/var/log/mail.log`   | All mail server related logs.                      |
| `/var/log/httpd`      | All Apache related logs.                           |
| `/var/log/mysqld.log` | All MySQL server related logs.                     |

```shell-session

for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done
```

## Memory and Cache

Tool called [mimipenguin](https://github.com/huntergregal/mimipenguin) that makes the whole process easier. However, this tool requires administrator/root permissions.

#### Memory - Mimipenguin

```shell-session
sudo python3 mimipenguin.py
```

```shell-session
 sudo bash mimipenguin.sh 
```

`LaZagne`.

The passwords and hashes we can obtain come from the following sources but are not limited to:

  
|   |   |   |   |
|---|---|---|---|
|Wifi|Wpa_supplicant|Libsecret|Kwallet|
|Chromium-based|CLI|Mozilla|Thunderbird|
|Git|Env_variable|Grub|Fstab|
|AWS|Filezilla|Gftp|SSH|
|Apache|Shadow|Docker|KeePass|
|Mimipy|Sessions|Keyrings|

`Keyrings` are used for secure storage and management of passwords on Linux distributions
Passwords are stored encrypted and protected with a master password. It is an OS-based password manager

#### Memory - LaZagne

```shell-session
sudo python2.7 laZagne.py all
```

#### Browsers

#### Firefox Stored Credentials

```shell-session
ls -l .mozilla/firefox/ | grep default 
```

```shell-session
cat .mozilla/firefox/1bplpd86.default-release/logins.json | jq .
```

The tool [Firefox Decrypt](https://github.com/unode/firefox_decrypt) is excellent for decrypting these credentials, and is updated regularly. It requires Python 3.9 to run the latest version. Otherwise, `Firefox Decrypt 0.7.0` with Python 2 must be used.

#### Decrypting Firefox Credentials

```shell-session
python3.9 firefox_decrypt.py
```

#### Browsers - LaZagne

```shell-session
python3 laZagne.py browsers
```


