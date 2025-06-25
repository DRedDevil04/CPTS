
## The Concept of the Attack

xp_dirtree : This function is used to view the contents of a specific folder (local or remote).

The interesting thing is that the MSSQL function `xp_dirtree` is not directly a vulnerability but takes advantage of the authentication mechanism of SMB. When we try to access a shared folder on the network with a Windows host, this Windows host automatically sends an `NTLMv2` hash for authentication.

SMB Relay attack where we "replay" the hash to log into other systems where the account has local admin privileges or `cracking` this hash on our local system. Successful cracking would allow us to see and use the password in cleartext.

#### Initiation of the Attack

|**Step**|**XP_DIRTREE**|**Concept of Attacks - Category**|
|---|---|---|
|`1.`|The source here is the user input, which specifies the function and the folder shared in the network.|`Source`|
|`2.`|The process should ensure that all contents of the specified folder are displayed to the user.|`Process`|
|`3.`|The execution of system commands on the MSSQL server requires elevated privileges with which the service executes the commands.|`Privileges`|
|`4.`|The SMB service is used as the destination to which the specified information is forwarded.|`Destination`|


| **Step** | **Stealing the Hash**                                                                                                       | **Concept of Attacks - Category** |
| -------- | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| `5.`     | Here, the SMB service receives the information about the specified order through the previous process of the MSSQL service. | `Source`                          |
| `6.`     | The data is then processed, and the specified folder is queried for the contents.                                           | `Process`                         |
| `7.`     | The associated authentication hash is used accordingly since the MSSQL running user queries the service.                    | `Privileges`                      |
| `8.`     | In this case, the destination for the authentication and query is the host we control and the shared folder on the network. | `Destination`                     |
hash is intercepted by tools like `Responder`, `WireShark`, or `TCPDump`
For example, another interesting method would be to execute Python code in a SQL query. We can find more about this in the [documentation](https://docs.microsoft.com/en-us/sql/machine-learning/tutorials/quickstart-python-create-script?view=sql-server-ver15) from Microsoft