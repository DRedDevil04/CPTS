The main components used for remote management of Windows and Windows servers are the following:

- Remote Desktop Protocol (`RDP`)
    
- Windows Remote Management (`WinRM`)
    
- Windows Management Instrumentation (`WMI`)

## RDP

Windows GUI

Port : TCP/3389
(can also use UDP/3389 for remote administration)

For RDP session, network and server firewall should allow connections from outside

If [Network Address Translation](https://en.wikipedia.org/wiki/Network_address_translation) (`NAT`) is used on the route between client and server, as is often the case with Internet connections, the remote computer needs the **public IP address** to reach the server. In addition, **port forwarding** must be set up on the NAT router in the direction of the server.

This service can be activated using the `Server Manager` and comes with the default setting to allow connections to the service only to hosts with [Network level authentication](https://en.wikipedia.org/wiki/Network_Level_Authentication) (`NLA`).

### Footprinting

#### nmap

```shell-session
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```

 `RDP cookies` (`mstshash=nmap`) used by Nmap to interact with the RDP server can be identified by `threat hunters` and various security services such as [Endpoint Detection and Response](https://en.wikipedia.org/wiki/Endpoint_detection_and_response) (`EDR`), and can lock us out as penetration testers on hardened networks.


A Perl script named [rdp-sec-check.pl](https://github.com/CiscoCXSecurity/rdp-sec-check) has also been developed by [Cisco CX Security Labs](https://github.com/CiscoCXSecurity) that can unauthentically identify the security settings of RDP servers based on the handshakes.

##### RDP Security Check - Installation

```shell-session
sudo cpan
```

In cpan:

```shell-session
install Encoding::BER
```


```shell-session
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
```

```shell-session
./rdp-sec-check.pl 10.129.201.248
```

##### Initiate an RDP Session

```shell-session
xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
```

---
### WinRM

uses the Simple Object Access Protocol (`SOAP`)

Ports: `TCP` ports `5985` and `5986` for communication, with the last port `5986 using HTTPS`

Windows Remote Shell (`WinRS`),

#### Footprinting

##### nmap

```shell-session
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```


##### Checking for WinRM

```shell-session
evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!
```

---

### WMI

Windows Management Instrumentation (`WMI`)

allows read and write access to almost all settings on Windows systems.

WMI is not a single program but consists of several programs and various databases, also known as repositories.

#### Footprinting

Port: TCP/135

### [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py)

```shell-session
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```
