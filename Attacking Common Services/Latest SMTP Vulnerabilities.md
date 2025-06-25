The technical details of this vulnerability can be found [here](https://www.openwall.com/lists/oss-security/2020/01/28/3).

#### Initiation of the Attack

|**Step**|**Remote Code Execution**|**Concept of Attacks - Category**|
|---|---|---|
|`1.`|The source is the user input that can be entered manually or automated during direct interaction with the service.|`Source`|
|`2.`|The service will take the email with the required information.|`Process`|
|`3.`|Listening to the standardized ports of a system requires `root` privileges on the system, and if these ports are used, the service runs accordingly with elevated privileges.|`Privileges`|
|`4.`|As the destination, the entered information is forwarded to another local process.|`Destination`|

This is when the cycle starts all over again, but this time to gain remote access to the target system.

#### Trigger Remote Code Execution

|**Step**|**Remote Code Execution**|**Concept of Attacks - Category**|
|---|---|---|
|`5.`|This time, the source is the entire input, especially from the sender area, which contains our system command.|`Source`|
|`6.`|The process reads all the information, and the semicolon (`;`) interrupts the reading due to special rules in the source code that leads to the execution of the entered system command.|`Process`|
|`7.`|Since the service is already running with elevated privileges, other processes of OpenSMTPD will be executed with the same privileges. With these, the system command we entered will also be executed.|`Privileges`|
|`8.`|The destination for the system command can be, for example, the network back to our host through which we get access to the system.|

[Rabbit](https://www.youtube.com/watch?v=5nnJq_IWJog), which deals with brute-forcing Outlook Web Access (OWA) and then sending a document with a malicious macro to phish a user, [SneakyMailer](https://0xdf.gitlab.io/2020/11/28/htb-sneakymailer.html) which has elements of phishing and enumerating a user's inbox using Netcat and an IMAP client, and [Reel](https://0xdf.gitlab.io/2018/11/10/htb-reel.html) which dealt with brute-forcing SMTP users and phishing with a malicious RTF file.

