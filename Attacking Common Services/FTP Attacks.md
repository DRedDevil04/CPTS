
The [File Transfer Protocol](https://en.wikipedia.org/wiki/File_Transfer_Protocol) (FTP) is a standard network protocol used to transfer files between computers.

## Enumeration
`Nmap` default scripts `-sC` includes the [ftp-anon](https://nmap.org/nsedoc/scripts/ftp-anon.html) Nmap script which checks if a FTP server allows anonymous logins

The version enumeration flag `-sV` provides interesting information about FTP services, such as the FTP banner, which often includes the version name. We can use the `ftp` client or `nc` to interact with the FTP service. By default, FTP runs on TCP port 21.

```shell-session
sudo nmap -sC -sV -p 21 192.168.2.142 
```

## Misconfigurations

#### Anonymous Authentication

```shell-session
ftp 192.168.2.142    
```

We can use the commands `ls` and `cd` to move around directories like in Linux. To download a single file, we use `get`, and to download multiple files, we can use `mget`. For upload operations, we can use `put` for a simple file or `mput` for multiple files. We can use `help` in the FTP client session for more information.

#### Brute Forcing

[Medusa](https://github.com/jmk-foofus/medusa). With `Medusa`, we can use the option `-u` to specify a single user to target, or you can use the option `-U` to provide a file with a list of usernames. The option `-P` is for a file containing a list of passwords. We can use the option `-M` and the protocol we are targeting (FTP) and the option `-h` for the target hostname or IP address.

Although we may find services vulnerable to brute force, most applications today prevent these types of attacks. A more effective method is Password Spraying.

#### Brute Forcing with Medusa

```shell-session

medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp 
```

#### FTP Bounce Attack

The attacker uses a `PORT` command to trick the FTP connection into running commands and getting information from a device other than the intended server.

[https://www.geeksforgeeks.org/what-is-ftp-bounce-attack/](https://www.geeksforgeeks.org/what-is-ftp-bounce-attack/)

The `Nmap` -b flag can be used to perform an FTP bounce attack:

```shell-session
nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2
```

Modern FTP servers include protections


