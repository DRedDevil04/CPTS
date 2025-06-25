### Intelligent Platform Management Interface

- set of standardised specifications for hardware-based host management systems used for system management and monitoring
- direct network connection to the system's hardware

IPMI is typically used in three ways:

- Before the OS has booted to modify BIOS settings
- When the host is fully powered down
- Access to a host after a system failure

It can also be used to:

- monitor a range of different things such as system temperature, voltage, fan status, and power supplies.
- querying inventory information, reviewing hardware logs, and alerting using [[SNMP]].

To function, IPMI requires the following components:

- Baseboard Management Controller (BMC) - A micro-controller and essential component of an IPMI
- Intelligent Chassis Management Bus (ICMB) - An interface that permits communication from one chassis to another
- Intelligent Platform Management Bus (IPMB) - extends the BMC
- IPMI Memory - stores things such as the system event log, repository store data, and more
- Communications Interfaces - local system interfaces, serial and LAN interfaces, ICMB and PCI Management Bus

### Footprinting

Port : UDP/623
Systems that use the IPMI protocol are called **Baseboard Management Controllers** (BMCs)
Implemented as embedded ARM systems running Linux, and connected directly to the host's motherboard.

Popular: HP iLO, Dell DRAC, and Supermicro IPMI

Gaining access to a BMC is nearly equivalent to physical access to a system
Some expose a web-based management console

#### nmap

```shell-session
sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local
```

#### Metasploit Module

```shell-session
use auxiliary/scanner/ipmi/ipmi_version 
```

Often find BMCs where the administrators have not changed the default password.

|Product|Username|Password|
|---|---|---|
|Dell iDRAC|root|calvin|
|HP iLO|Administrator|randomized 8-character string consisting of numbers and uppercase letters|
|Supermicro IPMI|ADMIN|ADMIN|

### Dangerous Settings

Can turn to a [flaw](http://fish2.com/ipmi/remote-pw-cracking.html) in the RAKP protocol in IPMI 2.0 if defaults dont work
- server sends a salted SHA1 or MD5 hash of the user's password to the client before authentication takes place
- These password hashes can then be cracked offline using a dictionary attack using `Hashcat` mode `7300`.
#### HP iLO

`hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u`

 tries all combinations of upper case letters and numbers for an eight-character password

To retrieve IPMI hashes, we can use the Metasploit [IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/) module.

### Metasploit Dumping Hashes

```shell-session
use auxiliary/scanner/ipmi/ipmi_dumphashes 
```


