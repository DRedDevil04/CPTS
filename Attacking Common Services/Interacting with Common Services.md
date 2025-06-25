
## File Share Services

## Server Message Block (SMB)

SMB is commonly used in Windows networks, and we will often find share folders in a Windows network.

#### Windows

Windows GUI
`[WINKEY] + [R]` -> open the Run dialog box and type the file share location, e.g.: 

`\\192.168.220.129\Finance\`

#### Windows CMD - DIR

```cmd-session
dir \\192.168.220.129\Finance\
```

The command [net use](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/gg651155(v=ws.11)) connects a computer to or disconnects a computer from a shared resource or displays information about computer connections. We can connect to a file share with the following command and map its content to the drive letter `n`.

```cmd-session
net use n: \\192.168.220.129\Finance
```

```cmd-session
net use n: \\192.168.220.129\Finance /user:plaintext Password123
```

With the shared folder mapped as the `n` drive, we can execute Windows commands as if this shared folder is on our local computer. Let's find how many files the shared folder and its subdirectories contain.

```cmd-session
dir n: /a-d /s /b | find /c ":\"
```

| **Syntax** | **Description**                                                |
| ---------- | -------------------------------------------------------------- |
| `dir`      | Application                                                    |
| `n:`       | Directory or drive to search                                   |
| `/a-d`     | `/a` is the attribute and `-d` means not directories           |
| `/s`       | Displays files in a specified directory and all subdirectories |
| `/b`       | Uses bare format (no heading information or summary)           |
With `dir` we can search for specific names in files such as:

- cred
- password
- users
- secrets
- key
- Common File Extensions for source code such as: .cs, .c, .go, .java, .php, .asp, .aspx, .html.

`dir /?` to see the full help.

```cmd-session
n:\*cred* /s /b
```

```cmd-session
n:\*secret* /s /b
```

If we want to search for a specific word within a text file, we can use [findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr).

#### Windows CMD - Findstr

```cmd-session
findstr /s /i cred n:\*.*
```

We can find more `findstr` examples [here](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr#examples).

#### Windows PowerShell

```powershell-session
Get-ChildItem \\192.168.220.129\Finance\
```

```powershell-session

New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" 
-PSProvider "FileSystem"
```

To provide a username and password with Powershell, we need to create a [PSCredential object](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.pscredential). It offers a centralized way to manage usernames, passwords, and credentials.

#### Windows PowerShell - PSCredential Object

```powershell-session
$username = 'plaintext'
```

```powershell-session
$password = 'Password123'
```

```powershell-session

$secpassword = ConvertTo-SecureString $password -AsPlainText -Force
```

```powershell-session

$cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
```

```powershell-session

New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred
```

In PowerShell, we can use the command `Get-ChildItem` or the short variant `gci` instead of the command `dir`.

#### Windows PowerShell - GCI

```powershell-session
N:
```

```powershell-session
(Get-ChildItem -File -Recurse | Measure-Object).Count
```

```powershell-session
Get-ChildItem -Recurse -Path N:\ -Include *cred* -File
```

#### Windows PowerShell - Select-String

```powershell-session
Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List
```

#### Linux

#### Linux - Mount

```shell-session
 sudo mkdir /mnt/Finance
```

```shell-session
sudo mount -t cifs -o username=plaintext,password=Password123,
domain=. //192.168.220.129/Finance /mnt/Finance
```

credential file.

```shell-session
mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile
```

Credential File Structure 

```txt
username=plaintext
password=Password123
domain=.
```

We need to install `cifs-utils` to connect to an SMB share folder. To install it we can execute from the command line `sudo apt install cifs-utils`.

#### Linux - Find

```shell-session
find /mnt/Finance/ -name *cred*
```

find files that contain the string `cred`:

```shell-session
grep -rn /mnt/Finance/ -ie cred
```


## Other Services

#### Email

#### Linux - Install Evolution

```shell-session
sudo apt-get install evolution
```

If an error appears when starting evolution indicating "bwrap: Can't create file at ...", use this command to start evolution `export WEBKIT_FORCE_SANDBOX=0 && evolution`.

#### Video - Connecting to IMAP and SMTP using Evolution

Click on the image below to see a short video demonstration.

[Video](https://www.youtube.com/watch?v=xelO2CiaSVs)

#### Databases

We have three common ways to interact with databases:

| No. | Tools/Applications                                                                 |
|-----|------------------------------------------------------------------------------------|
| 1.  | Command Line Utilities (`mysql` or `sqsh`)                                        |
| 2.  | Programming Languages                                                             |
| 3.  | A GUI application to interact with databases such as HeidiSQL, MySQL Workbench, or SQL Server Management Studio. |

## Command Line Utilities
To interact with [MSSQL (Microsoft SQL Server)](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) with Linux we can use [sqsh](https://en.wikipedia.org/wiki/Sqsh) or [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility) if you are using Windows. `Sqsh` is much more than a friendly prompt. It is intended to provide much of the functionality provided by a command shell, such as variables, aliasing, redirection, pipes, back-grounding, job control, history, command substitution, and dynamic configuration. We can start an interactive SQL session as follows:

#### Linux - SQSH

```shell-session
 sqsh -S 10.129.20.13 -U username -P Password123
```


The `sqlcmd` utility lets you enter Transact-SQL statements, system procedures, and script files through a variety of available modes:

- At the command prompt.
- In Query Editor in SQLCMD mode.
- In a Windows script file.
- In an operating system (Cmd.exe) job step of a SQL Server Agent job.


#### Windows - SQLCMD

```cmd-session
sqlcmd -S 10.129.20.13 -U username -P Password123
```

#### MySQL

To interact with [MySQL](https://en.wikipedia.org/wiki/MySQL), we can use MySQL binaries for Linux (`mysql`) or Windows (`mysql.exe`).

#### Linux - MySQL
```shell-session
mysql -u username -pPassword123 -h 10.129.20.13
```

#### Windows - MySQL

```cmd-session
mysql.exe -u username -pPassword123 -h 10.129.20.13
```

[dbeaver](https://github.com/dbeaver/dbeaver) is a multi-platform database tool for Linux, macOS, and Windows that supports connecting to multiple database engines such as MSSQL, MySQL, PostgreSQL, among others, making it easy for us, as an attacker, to interact with common database servers.

To install [dbeaver](https://github.com/dbeaver/dbeaver) using a Debian package we can download the release .deb package from [https://github.com/dbeaver/dbeaver/releases](https://github.com/dbeaver/dbeaver/releases) and execute the following command:

#### Install dbeaver

```shell-session
sudo dpkg -i dbeaver-<version>.deb
```

#### Run dbeaver

```shell-session
dbeaver &
```

#### Video - Connecting to MSSQL DB using dbeaver

Click on the image below for a short video demonstration of connecting to an MSSQL database using `dbeaver`.

[MSSQL Video in dbeaver](https://www.youtube.com/watch?v=gU6iQP5rFMw)

Click on the image below for a short video demonstration of connecting to a MySQL database using `dbeaver`.

#### Video - Connecting to MySQL DB using dbeaver

[MySQL Dbeaver video](https://www.youtube.com/watch?v=PeuWmz8S6G8)

Once we have access to the database using a command-line utility or a GUI application, we can use

#### Tools to Interact with Common Services

|**SMB**|**FTP**|**Email**|**Databases**|
|---|---|---|---|
|[smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html)|[ftp](https://linux.die.net/man/1/ftp)|[Thunderbird](https://www.thunderbird.net/en-US/)|[mssql-cli](https://github.com/dbcli/mssql-cli)|
|[CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec)|[lftp](https://lftp.yar.ru/)|[Claws](https://www.claws-mail.org/)|[mycli](https://github.com/dbcli/mycli)|
|[SMBMap](https://github.com/ShawnDEvans/smbmap)|[ncftp](https://www.ncftp.com/)|[Geary](https://wiki.gnome.org/Apps/Geary)|[mssqlclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)|
|[Impacket](https://github.com/SecureAuthCorp/impacket)|[filezilla](https://filezilla-project.org/)|[MailSpring](https://getmailspring.com/)|[dbeaver](https://github.com/dbeaver/dbeaver)|
|[psexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py)|[crossftp](http://www.crossftp.com/)|[mutt](http://www.mutt.org/)|[MySQL Workbench](https://dev.mysql.com/downloads/workbench/)|
|[smbexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py)||[mailutils](https://mailutils.org/)|[SQL Server Management Studio or SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)|
|||[sendEmail](https://github.com/mogaal/sendemail)||
|||[swaks](http://www.jetmore.org/john/code/swaks/)||
|||[sendmail](https://en.wikipedia.org/wiki/Sendmail)||


Some reasons why we may not have access to a resource:

- Authentication
- Privileges
- Network Connection
- Firewall Rules
- Protocol Support