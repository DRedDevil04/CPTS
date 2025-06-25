
Upon initial logon, LSASS will:

- Cache credentials locally in memory
- Create [access tokens](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
- Enforce security policies
- Write to Windows [security log](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-logging-security)

## Dumping LSASS Process Memory

#### Task Manager Method

`Open Task Manager` > `Select the Processes tab` > `Find & right click the Local Security Authority Process` > `Select Create dump file`

A file called `lsass.DMP` is created and saved in:
```cmd-session
C:\Users\loggedonusersdirectory\AppData\Local\Temp
```

#### Rundll32.exe & Comsvcs.dll Method

command-line utility method

**NOTE**: Modern anti-virus tools recognise this method as malicious activity.

##### Finding LSASS PID in cmd

```cmd-session
tasklist /svc
```

find lsass.exe and its process ID in the PID field.

##### Finding LSASS PID in PowerShell

```powershell-session
Get-Process lsass
```

##### Creating lsass.dmp using PowerShell

 Elevated PowerShell session:

```powershell-session

rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

With this command, we are running `rundll32.exe` to call an exported function of `comsvcs.dll` which also calls the MiniDumpWriteDump (`MiniDump`) function to dump the LSASS process memory to a specified directory (`C:\lsass.dmp`)

---
If there were any active logon sessions, the credentials used to establish them will be present
## Using Pypykatz to Extract Credentials

#### Running Pypykatz

We use `lsa` in the command because LSASS is a subsystem of `local security authority`, then we specify the data source as a `minidump` file, proceeded by the path to the dump file (`/home/peter/Documents/lsass.dmp`) stored on our attack host. Pypykatz parses the dump file and outputs the findings:

```shell-session
pypykatz lsa minidump /home/peter/Documents/lsass.dmp 
```


#### Output Analysis

##### MSV
[MSV](https://docs.microsoft.com/en-us/windows/win32/secauthn/msv1-0-authentication-package) is an authentication package in Windows that LSA calls on to validate logon attempts against the SAM database.
Pypykatz extracted the:
- `SID`
- `Username`
- `Domain`
- `NT` & `SHA1` password hashes 
associated with the bob user account's logon session stored in LSASS process memory.

##### WDIGEST
`WDIGEST` is an older authentication protocol enabled by default in `Windows XP` - `Windows 8` and `Windows Server 2003` - `Windows Server 2012`
SASS caches credentials used by WDIGEST in **clear-text.**
Modern Windows operating systems have WDIGEST disabled by default.

Additionally, it is essential to note that Microsoft released a security update for systems affected by this issue with WDIGEST. We can study the details of that security update [here](https://msrc-blog.microsoft.com/2014/06/05/an-overview-of-kb2871997/).


##### Kerberos
network authentication protocol used by Active Directory in Windows Domain environments

Domain user accounts are granted tickets upon authentication with Active Directory

access resources without needing to type their credentials each time

LSASS `caches 
- passwords
- ekeys, 
- tickets
- pins
associated with Kerberos. It is possible to extract these from LSASS process memory and use them to access other systems joined to the same domain.

##### DPAPI
Data Protection Application Programming Interface or [DPAPI](https://docs.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection) is a set of APIs in Windows operating systems used to encrypt and decrypt DPAPI data blobs on a per-user basis for Windows OS features and various third-party applications. Here are just a few examples of applications that use DPAPI and what they use it for:

|Applications|Use of DPAPI|
|---|---|
|`Internet Explorer`|Password form auto-completion data (username and password for saved sites).|
|`Google Chrome`|Password form auto-completion data (username and password for saved sites).|
|`Outlook`|Passwords for email accounts.|
|`Remote Desktop Connection`|Saved credentials for connections to remote machines.|
|`Credential Manager`|Saved credentials for accessing shared resources, joining Wireless networks, VPNs and more.|
kMimikatz and Pypykatz can extract the DPAPI `masterkey` for the logged-on user whose data is present in LSASS process memory. This masterkey can then be used to decrypt the secrets associated with each of the applications using DPAPI and result in the capturing of credentials for various accounts. DPAPI attack techniques are covered in greater detail in the [Windows Privilege Escalation](https://academy.hackthebox.com/module/details/67) module.

---
#### Cracking the NT Hash with Hashcat

```shell-session
sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt
```

