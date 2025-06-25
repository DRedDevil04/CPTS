#### Simple Network Management Protocol

---

Uses:
- monitor network
- config tasks and changing settings
- udp port 161 for commands using agents
- Traps over udp 162
- Traps : data packets from SNMP server to client without a request

---

#### Terms
1. MID (**Management Information Base**):
	- independent format for storing device information
	- text file in which all queryable SNMP objects of a device are listed in a standardized tree hierarchy
	- contains at least one `Object Identifier` (`OID`), which, in addition to the necessary unique address and a name, also provides information about the type, access rights, and a description of the respective object
	- `Abstract Syntax Notation One` (`ASN.1`)
1. OID (**Object Identifier**): 
	- node in a hierarchical namespace(tree)
	- longer the chain, the more specific the information.
	- MIBs for the associated OIDs in the [Object Identifier Registry](https://www.alvestrand.no/objectid/).
	- Structure like : 1.2.3.1.8, like used in LaTeX
2. SNMPv1:
	- retrieval of information from network devices
	- allows for the configuration of devices
	- provides traps, which are notifications of events.
	- `no built-in authentication` mechanism, meaning anyone accessing the network can read and modify network data. 
	- `does not support encryption`
3. SNMPv2
	- additional functions
4. SNMPv3:
	- authentication
	- encryption using pre-shared key
5. Community Strings:
	- can be seen as passwords
	- used to determine whether the requested information can be viewed or not

---

#### Default Configuration

##### Daemon Config

```shell-session
cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'
```

Look at man pages for more idea of individual setting
and set up SNMP on VM

---

#### Dangerous Settings

| Settings                                       | Description                                                                           |
| ---------------------------------------------- | ------------------------------------------------------------------------------------- |
| rwuser noauth                                  | Provides access to the full OID tree without authentication.                          |
| rwcommunity <community string> <IPv4 address>  | Provides access to the full OID tree regardless of where the requests were sent from. |
| rwcommunity6 <community string> <IPv6 address> | Same access as with `rwcommunity` with the difference of using IPv6.                  |

---

#### Footprinting

##### SNMPwalk:

```shell-session
snmpwalk -v2c -c public 10.129.14.128
```

Works when misconfig, reads MIB. (need to supply version)

##### OneSixtyOne:

If community strings not known, brutforce using

```shell-session
apt install onesixtyone
```

```shell-session

onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128

```

##### Braa:

When know community string, brutforce OID using braa and enumerate info behind.

```shell-session
sudo apt install braa
```
```shell-session
braa <community string>@<IP>:.1.3.6.*   # Syntax
```

Usage:
```shell-session
braa public@10.129.14.128:.1.3.6.*
```


### Lab Practical

task 1:  obtain the email address of the admin

task 2: customized version of the SNMP server

task 3: custom script that is running on the system and submit its output as the answer.

---
### Extra Notes

DBSNMP service uses a default password.