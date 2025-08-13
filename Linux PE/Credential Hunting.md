```shell
grep 'DB_USER\|DB_PASSWORD' wp-config.php
```

```shell
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```

## SSH Keys

It is also useful to search around the system for accessible SSH private keys. We may locate a private key for another, more privileged, user that we can use to connect back to the box with additional privileges. We may also sometimes find SSH keys that can be used to access other hosts in the environment. Whenever finding SSH keys check the `known_hosts` file to find targets. This file contains a list of public keys for all the hosts which the user has connected to in the past and may be useful for lateral movement or to find data on a remote host that can be used to perform privilege escalation on our target.

```shell
ls ~/.ssh
```

