
`OS Version`: Knowing the distribution (Ubuntu, Debian, FreeBSD, Fedora, SUSE, Red Hat, CentOS, etc.) will give you an idea of the types of tools that may be available.

`Kernel Version`: As with the OS version, there may be public exploits that target a vulnerability in a specific kernel version. Kernel exploits can cause system instability or even a complete crash. Be careful running these against any production system, and make sure you fully understand the exploit and possible ramifications before running one.

`Running Services`: Knowing what services are running on the host is important, especially those running as root. A misconfigured or vulnerable service running as root can be an easy win for privilege escalation.

## Gaining Situational Awareness

Typically we'll want to run a few basic commands to orient ourselves:

- `whoami` - what user are we running as
- `id` - what groups does our user belong to?
- `hostname` - what is the server named, can we gather anything from the naming convention?
- `ifconfig` or `ip a` - what subnet did we land in, does the host have additional NICs in other subnets?
- `sudo -l` - can our user run anything with sudo (as another user as root) without needing a password? This can sometimes be the easiest win and we can do something like `sudo su` and drop right into a root shell.

```shell-session

cat /etc/os-release
```

We can see that the target is running [Ubuntu 20.04.4 LTS ("Focal Fossa")](https://releases.ubuntu.com/20.04/). For whatever version we encounter its important to see if we're dealing with something out-of-date or maintained. Ubuntu publishes its [release cycle](https://ubuntu.com/about/release-cycle) and from this we can see that "Focal Fossa" does not reach end of life until April 2030. From this information we can assume that we will not encounter a well-known Kernel vulnerability because the customer has been keeping their internet-facing asset patched but we'll still look regardless.

Next we'll want to check out our current user's PATH, which is where the Linux system looks every time a command is executed for any executables to match the name of what we type, i.e., `id` which on this system is located at `/usr/bin/id`. As we'll see later in this module, if the PATH variable for a target user is misconfigured we may be able to leverage it to escalate privileges

```shell-session

echo $PATH
```

```shell-session

env
```

```shell-session
uname -a
cat /proc/version
```

CPU Info:
```shell-session

lscpu 
```

```shell-session

cat /etc/shells
```

We should also check to see if any defenses are in place and we can enumerate any information about them. Some things to look for include:

- [Exec Shield](https://en.wikipedia.org/wiki/Exec_Shield)
- [iptables](https://linux.die.net/man/8/iptables)
- [AppArmor](https://apparmor.net/)
- [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)
- [Fail2ban](https://github.com/fail2ban/fail2ban)
- [Snort](https://www.snort.org/faq/what-is-snort)
- [Uncomplicated Firewall (ufw)](https://wiki.ubuntu.com/UncomplicatedFirewall)
  
  Block devices on the system (hard disks, USB drives, optical drives, etc.). If we discover and can mount an additional drive or unmounted file system, we may find sensitive files, passwords, or backups that can be leveraged to escalate privileges.
  
```shell-session
lsblk
```

`lpstat` can be used to find information about any printers attached to the system. If there are active or queued print jobs can we gain access to some sort of sensitive information?

We should also check for mounted drives and unmounted drives. Can we mount an umounted drive and gain access to sensitive data? Can we find any types of credentials in `fstab` for mounted drives by grepping for common words such as password, username, credential, etc in `/etc/fstab`


```shell-session
cat /etc/fstab
```

Routing Table: 

 `route` or `netstat -rn`

ARP Table:
 ```shell-session
arp -a
```

```shell-session
cat /etc/passwd
```

Occasionally, we will see password hashes directly in the `/etc/passwd` file. This file is readable by all users, and as with hashes in the `/etc/shadow` file, these can be subjected to an offline password cracking attack. This configuration, while not common, can sometimes be seen on embedded devices and routers.

```shell-session

cat /etc/passwd | cut -f1 -d:
```


With Linux, several different hash algorithms can be used to make the passwords unrecognizable. Identifying them from the first hash blocks can help us to use and work with them later if needed. Here is a list of the most used ones:

|**Algorithm**|**Hash**|
|---|---|
|Salted MD5|`$1$`...|
|SHA-256|`$5$`...|
|SHA-512|`$6$`...|
|BCrypt|`$2a$`...|
|Scrypt|`$7$`...|
|Argon2|`$argon2i$`...|

We'll also want to check which users have login shells.

Because outdated versions, such as Bash version 4.1, are vulnerable to a `shellshock` exploit.

```shell-session
 cat /etc/group
```
List Users in a group:
```shell-session
getent group sudo
```

Its also important to check for SSH keys for all users, as these could be used to achieve persistence on the system, potentially to escalate privileges, or to assist with pivoting and port forwarding further into the internal network. At the minimum, check the ARP cache to see what other hosts are being accessed and cross-reference these against any useable SSH private keys.

```shell-session
ls /home
```

Finally, we can search for any "low hanging fruit" such as config files, and other files that may contain sensitive information. Configuration files can hold a wealth of information. It is worth searching through all files that end in extensions such as .conf and .config, for usernames, passwords, and other secrets.

#### Mounted File Systems

```shell-session
df -h
```

#### Unmounted File Systems

```shell-session
cat /etc/fstab | grep -v "#" | column -t
```


#### All Hidden Files
```shell-session

find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student
```

#### All Hidden Directories

```shell-session

find / -type d -name ".*" -ls 2>/dev/null
```

The data retention time for `/var/tmp` is much longer than that of the `/tmp` directory. By default, all files and data stored in /var/tmp are retained for up to 30 days. In /tmp, on the other hand, the data is automatically deleted after ten days.

In addition, all temporary files stored in the `/tmp` directory are deleted immediately when the system is restarted. Therefore, the `/var/tmp` directory is used by programs to store data that must be kept between reboots temporarily.

#### Temporary Files
```shell-session

ls -l /tmp /var/tmp /dev/shm
```

