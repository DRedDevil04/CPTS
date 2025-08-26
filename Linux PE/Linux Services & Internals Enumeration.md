
## Internals

#### Network Interfaces

```shell
ip a
```

#### Hosts

```shell
cat /etc/hosts
```

#### User's Last Login

```shell
lastlog
```

let's see if anyone else is currently on the system with us. There are a few ways to do this, such as the `who` command. The `finger` command will work to display this information on some Linux systems. We can see that the `cliff.moore` user is logged in to the system with us.

#### Logged In Users

```shell-session
w
```

#### Command History

```shell

find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
```

#### Cron

```shell
ls -la /etc/cron.daily/
```

The [proc filesystem](https://man7.org/linux/man-pages/man5/proc.5.html) (`proc` / `procfs`) is a particular filesystem in Linux that contains information about system processes, hardware, and other system information. It is the primary way to access process information and can be used to view and modify kernel settings.


#### Proc
```shell

 find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
```

## Services

 To do this, we first need to create a list of installed packages to work with.

#### Installed Packages

```shell

apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
```

#### Sudo Version

```shell
sudo -V
```
#### Binaries

```shell
ls -l /bin /usr/bin/ /usr/sbin/
```

With the next oneliner, we can compare the existing binaries with the ones from GTFObins to see which binaries we should investigate later.

```shell

for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done
```

We can use the diagnostic tool `strace` on Linux-based operating systems to track and analyze system calls and signal processing. It allows us to follow the flow of a program and understand how it accesses system resources, processes signals, and receives and sends data from the operating system. In addition, we can also use the tool to monitor security-related activities and identify potential attack vectors, such as specific requests to remote hosts using passwords or tokens.

#### Configuration Files

```bash
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
```

#### Scripts

```shell

find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```

#### Running Services by User

```shell
ps aux | grep root
```

