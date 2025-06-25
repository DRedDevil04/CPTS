
 [CVE-2019-0708](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2019-0708). This vulnerability is known as `BlueKeep`. It does not require prior access to the system to exploit the service for our purposes. However, the exploitation of this vulnerability led and still leads to many malware or ransomware attacks.
 
---
## The Concept of the Attack

The vulnerability occurs after initializing the connection when basic settings are exchanged between client and server. This is known as a [Use-After-Free](https://cwe.mitre.org/data/definitions/416.html) (`UAF`) technique that uses freed memory to execute arbitrary code.

If we want to look at the technical analysis of the BlueKeep vulnerability, this [article](https://unit42.paloaltonetworks.com/exploitation-of-windows-cve-2019-0708-bluekeep-three-ways-to-write-data-into-the-kernel-with-rdp-pdu/) provides a nice overview.

#### Initiation of the Attack

|**Step**|**BlueKeep**|**Concept of Attacks - Category**|
|---|---|---|
|`1.`|Here, the source is the initialization request of the settings exchange between server and client that the attacker has manipulated.|`Source`|
|`2.`|The request leads to a function used to create a virtual channel containing the vulnerability.|`Process`|
|`3.`|Since this service is suitable for [administering](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account) of the system, it is automatically run with the [LocalSystem Account](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account) privileges of the system.|`Privileges`|
|`4.`|The manipulation of the function redirects us to a kernel process.|`Destination`|

Note: This is a flaw that we will likely run into during our penetration tests, but it can cause system instability, including a "blue screen of death (BSoD)," and we should be careful before using the associated exploit. If in doubt, it's best to first speak with our client so they understand the risks and then decide if they would like us to run the exploit or not.

