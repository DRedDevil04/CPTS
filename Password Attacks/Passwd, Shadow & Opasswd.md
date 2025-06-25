
Linux-based distributions can use many different authentication mechanisms. One of the most commonly used and standard mechanisms is [Pluggable Authentication Modules](https://web.archive.org/web/20220622215926/http://www.linux-pam.org/Linux-PAM-html/Linux-PAM_SAG.html) (`PAM`). The modules used for this are called `pam_unix.so` or `pam_unix2.so` and are located in `/usr/lib/x86_x64-linux-gnu/security/` in Debian based distributions. These modules manage :
- user information
- authentication
- sessions
- current passwords
- old passwords.
PAM also has many other service modules, such as LDAP, mount, or Kerberos.


## Passwd File

#### Passwd Format

|`cry0l1t3`|`:`|`x`|`:`|`1000`|`:`|`1000`|`:`|`cry0l1t3,,,`|`:`|`/home/cry0l1t3`|`:`|`/bin/bash`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|Login name||Password info||UID||GUID||Full name/comments||Home directory|

Modern systems have the hash values stored in the `/etc/shadow`

Usually, we find the value `x` in this field, which means that the passwords are stored in an encrypted form in the `/etc/shadow` file. However, it can also be that the `/etc/passwd` file is writeable by mistake. This would allow us to clear this field for the user `root` so that the password info field is empty. This will cause the system not to send a password prompt when a user tries to log in as `root`.


#### Editing /etc/passwd - Before

```shell-session
root:x:0:0:root:/root:/bin/bash
```

#### Editing /etc/passwd - After

```shell-session
root::0:0:root:/root:/bin/bash
```

#### Root without Password

```shell-session
su
```

---

## Shadow File

It contains all the password information for the created users. For example, if there is no entry in the `/etc/shadow` file for a user in `/etc/passwd`, the user is considered invalid. The `/etc/shadow` file is also only readable by users who have administrator rights. The format of this file is divided into `nine fields`:

#### Shadow Format

|`cry0l1t3`|`:`|`$6$wBRzy$...SNIP...x9cDWUxW1`|`:`|`18937`|`:`|`0`|`:`|`99999`|`:`|`7`|`:`|`:`|`:`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|Username||Encrypted password||Last PW change||Min. PW age||Max. PW age||Warning period|Inactivity period|Expiration date|Unused|

If the password field contains a character, such as `!` or `*`, the user cannot log in with a Unix password.
However, other authentication methods for logging in, such as Kerberos or key-based authentication, can still be used. The same case applies if the `encrypted password` field is empty. This means that no password is required for the login. However, it can lead to specific programs denying access to functions. The `encrypted password` also has a particular format by which we can also find out some information:

- `$<type>$<salt>$<hashed>`

#### Algorithm Types

- `$1$` – MD5
- `$2a$` – Blowfish
- `$2y$` – Eksblowfish
- `$5$` – SHA-256
- `$6$` – SHA-512

By default, the SHA-512 (`$6$`) encryption method is used on the latest Linux distributions

---

## Opasswd

The PAM library (`pam_unix.so`) can prevent reusing old passwords. The file where old passwords are stored is the `/etc/security/opasswd`. Administrator/root permissions are also required to read the file if the permissions for this file have not been changed manually.

#### Reading /etc/security/opasswd

```shell-session
sudo cat /etc/security/opasswd
```

MD5 used so easy to crack

---
## Cracking Linux Credentials

#### Unshadow

```shell-session
 sudo cp /etc/passwd /tmp/passwd.bak 
```
```shell-session
 sudo cp /etc/shadow /tmp/shadow.bak 
```

```shell-session
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
```

#### Hashcat - Cracking Unshadowed Hashes

```shell-session
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```

#### Hashcat - Cracking MD5 Hashes

```shell-session
hashcat -m 500 -a 0 md5-hashes.list rockyou.txt
```

