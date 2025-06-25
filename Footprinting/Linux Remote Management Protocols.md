
## SSH

Port : TCP/22

`SSH-2`, also known as SSH version 2, is a more advanced protocol than SSH version 1 in encryption, speed, stability, and security. For example, `SSH-1` is vulnerable to `MITM` attacks, whereas SSH-2 is not.

OpenSSH has six different authentication methods:

1. Password authentication
2. Public-key authentication
3. Host-based authentication
4. Keyboard authentication
5. Challenge-response authentication
6. GSSAPI authentication

 learn more about the other authentication methods [here](https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/) among others.

#### Default Configuration

 [sshd_config](https://www.ssh.com/academy/ssh/sshd_config)

default configuration includes X11 forwarding, which contained a command injection vulnerability in version 7.2p1 of OpenSSH in 2016. Nevertheless, we do not need a GUI to manage our servers.

#### Dangerous Settings

| **Setting**                  | **Description**                             |
| ---------------------------- | ------------------------------------------- |
| `PasswordAuthentication yes` | Allows password-based authentication.       |
| `PermitEmptyPasswords yes`   | Allows the use of empty passwords.          |
| `PermitRootLogin yes`        | Allows to log in as the root user.          |
| `Protocol 1`                 | Uses an outdated version of encryption.     |
| `X11Forwarding yes`          | Allows X11 forwarding for GUI applications. |
| `AllowTcpForwarding yes`     | Allows forwarding of TCP ports.             |
| `PermitTunnel`               | Allows tunneling.                           |
| `DebianBanner yes`           | Displays a specific banner when logging in. |

[hardening guides](https://www.ssh-audit.com/hardening_guides.html)

#### Footprinting

##### [ssh-audit](https://github.com/jtesta/ssh-audit)
checks the client-side and server-side configuration and shows some general information and which encryption algorithms are still used by the client and server.


```shell-session
git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
```

```shell-session
./ssh-audit.py 10.129.14.132
```

The previous versions had some vulnerabilities, such as [CVE-2020-14145](https://www.cvedetails.com/cve/CVE-2020-14145/), which allowed the attacker the capability to Man-In-The-Middle and attack the initial connection attempt.

##### Change Authentication Method

```shell-session
ssh -v cry0l1t3@10.129.14.132
```

```shell-session
 ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password
```

---
## Rsync

[Rsync](https://linux.die.net/man/1/rsync) is a fast and efficient tool for locally and remotely copying files.

- delta-transfer algorithm.
- sending only the differences between the source files and the older version of the files that reside on the destination server.
- backups and mirroring
- can be configured to use SSH for secure file transfers by piggybacking on top of an established SSH server connection

Port : TCP/873

Pentesting Rsync: [guide](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync)

#### Footprinting

##### Scanning for Rsync

```shell-session
sudo nmap -sV -p 873 127.0.0.1
```

##### Probing for Accessible Shares

```shell-session
 nc -nv 127.0.0.1 873
```

##### Enumerating an Open Share

```shell-session
rsync -av --list-only rsync://127.0.0.1/dev
```

 From here, we could sync all files to our attack host with the command 
 
 `rsync -av rsync://127.0.0.1/dev`

for rsync configured to use ssh to transfer files, can use

 `-e ssh` flag, or `-e "ssh -p2222"`

Understanding syntax: [guide](https://phoenixnap.com/kb/how-to-rsync-over-ssh)

---
## R-Services

R-Services are a suite of services hosted to enable remote access or issue commands between Unix hosts over TCP/IP

Much like `telnet`, r-services transmit information from client to server(and vice versa) over the network in an unencrypted format, making it possible for attackers to intercept network traffic (passwords, login information, etc.) by performing man-in-the-middle (`MITM`) attacks.

Ports:  `512`, `513`, and `514`

Accessible through suite of commands known as `r-commands`

Commonly used by commercial operating systems such as:
- Solaris
- HP-UX
- AIX

The [R-commands](https://en.wikipedia.org/wiki/Berkeley_r-commands) suite consists of the following programs:

- rcp (`remote copy`)
- rexec (`remote execution`)
- rlogin (`remote login`)
- rsh (`remote shell`)
- rstat
- ruptime
- rwho (`remote who`)


| **Command** | **Service Daemon** | **Port** | **Transport Protocol** | **Description**                                                                                                                                                                                                                                                            |
| ----------- | ------------------ | -------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rcp`       | `rshd`             | 514      | TCP                    | Copy a file or directory bidirectionally from the local system to the remote system (or vice versa) or from one remote system to another. It works like the `cp` command on Linux but provides `no warning to the user for overwriting existing files on a system`.        |
| `rsh`       | `rshd`             | 514      | TCP                    | Opens a shell on a remote machine without a login procedure. Relies upon the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files for validation.                                                                                                                 |
| `rexec`     | `rexecd`           | 512      | TCP                    | Enables a user to run shell commands on a remote machine. Requires authentication through the use of a `username` and `password` through an unencrypted network socket. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files. |
| `rlogin`    | `rlogind`          | 513      | TCP                    | Enables a user to log in to a remote host over the network. It works similarly to `telnet` but can only connect to Unix-like hosts. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files.                                     |

The /etc/hosts.equiv file contains a list of trusted hosts and is used to grant access to other systems on the network. When users on one of these hosts attempt to access the system, they are automatically granted access without further authentication.


###### /etc/hosts.equiv

```shell-session
DarthTellectus@htb[/htb]$ cat /etc/hosts.equiv

# <hostname> <local username>
pwnbox cry0l1t3
```

#### Footprinting

##### nmap

```shell-session
sudo nmap -sV -p 512,513,514 10.0.17.2
```

##### Access Control & Trusted Relationships
 
 By default, these services utilize [Pluggable Authentication Modules (PAM)](https://debathena.mit.edu/trac/wiki/PAM) for user authentication onto a remote system; however, they also bypass this authentication through the use of the `/etc/hosts.equiv` and `.rhosts` files on the system. The `hosts.equiv` and `.rhosts` files contain a list of hosts (`IPs` or `Hostnames`) and users that are `trusted` by the local host when a connection attempt is made using `r-commands`. Entries in either file can appear like the following:

 The `hosts.equiv` file is recognized as the global configuration regarding all users on a system, whereas `.rhosts` provides a per-user configuration.

##### Logging in Using Rlogin

```shell-session
rlogin 10.0.17.2 -l htb-student
```

rwho : list all interactive sessions on the local network

```shell-session
 rwho
```

`rwho` daemon periodically broadcasts information about logged-on users, so it might be beneficial to watch the network traffic.

##### Listing Authenticated Users Using Rusers

detailed account of all logged-in users over the network, including information such as the username, hostname of the accessed machine, TTY that the user is logged in to, the date and time the user logged in, the amount of time since the user typed on the keyboard, and the remote host they logged in from (if applicable).

```shell-session
rusers -al 10.0.17.5
```


















