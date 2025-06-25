
- OS Version
- Kernel Version
- Running Services
- Current Processes
- Installed Packages and Version
- Logged in Users
- User Home Directories
- .bash_history
- ssh keys
SSH keys could be leveraged to access other systems within the network as well. At the minimum, check the ARP cache to see what other hosts are being accessed and cross-reference these against any useable SSH private keys.

- ssh dir content(~/.ssh)
- history
- sudo -l
- configuration files (.conf, .config)
- shadow file
- hashes may be in /etc/passwd(seen on embedded systems)
- cron jobs(
ls -la /etc/cron.daily/)
- unmounted fs and additional drives(lsblk)
- suid and sgid permissions
- writable dirs(find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null)
- writeable files
```shell-session
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```
- 