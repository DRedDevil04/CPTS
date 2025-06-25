- client based protocol
- port 25
- fetch emails and send emails
- combied wiht POP3 or IMAP
- can be between 2 SMTP servers and  sever and client aswell
---
- newer servers use port 587 : used for mails from authenticated users and servers (using STARTTLS) 
- in this, client -> password and username to server and emails can now be transmitted
- client then sends `sender` and `recipients` addrsses and the `contents, parameters and other arguments`
- connection terminated afterwards
---
- sometimes poert 465 used for ssl tls connections

![[Screenshot 2024-12-23 at 4.29.03 PM.png]]
MUA : *Mail User Agent* : converts to header and body and sends to SMTP server **CLIENT**
MSA: *Mail SUbmission Agent*: check validity (orgin) of the mail **SERVER**
MTA: *Mail Transfer Agent*: sending and receiving emails **SERVER**
MDA : *Mail Delivery Agent*:  Transfers mail to recievers Mailbox **Destination Server**

### Disadvantages
- NO Delivery Confirmation
- only english language erro message includeing header of the undelivered message returned
- users not authenticated when connection established, *sender unreliable* 
Nowadays ESMTP only used: TLS, Authentication by plugin

---
# Interacting With SMTP and Fingerprinting

### Telnet
`
`telnet 10.129.14.128 25`

then use commands to perform actions

![[Screenshot 2024-12-23 at 5.00.48 PM.png]]

- VRFY can be used to enumerate users on the system, doesnt always work
- 252 code-> may confirm existence of non existing user
 Note: Webproxy connect to SMTP:
 
 `CONNECT 10.129.14.128:25 HTTP/1.0`

Header: 
- sender
- recipient
- time of sending and arrival
- stations on the way
- content and format of the message

## Dangerous Settings

- To prevent the sent emails from being filtered by spam filters and not reaching the recipient, the sender can use a relay server that the recipient trusts. (bypass MSA (Submission agent))
- Allowing all IP addresses.

 ```shell-session
mynetworks = 0.0.0.0/0
```

Allowing all IPs, leads to this SMTP server vulnerable to **Fake Mails** and **connection initialazation with multiple parties**. Also leads to **spoofing** mails and reading them.

## Footprinting the Service

### Nmap

````shell-session
sudo nmap 10.129.14.128 -sC -sV -p25
````

SMTP open-relay NSE script

```shell-session
sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v
```

## Practice Lab

telnet:
HELO -> gives Hostname
SMTP username enumration using smtp-user-enum :

`smtp-user-enum -M VRFY -U ../footprinting-wordlist.txt -t 10.129.25.219 -w 20`

-w: waittime for each request 
-U: user wordlist
-t: smtp ip
-M: Method

