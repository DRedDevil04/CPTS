
## IP Addressing & NICs

Static IP assignment is common with:

- Servers
- Routers
- Switch virtual interfaces
- Printers
- And any devices that are providing critical services to the network

IP address is assigned to a `Network Interface Controller` (`NIC`)

#### Using ifconfig

```shell-session
ifconfig
```

Output Analysis:

- each NIC has an identifier (`eth0`, `eth1`, `lo`, `tun0`)
- addressing information
- traffic statistics
- tun0 -> VPN connection

VPN encrypts traffic and also establishes a tunnel over a public network (often the Internet), through `NAT` on a public-facing network appliance, and into the internal/private network.

We will see public IPs on devices that are directly facing the Internet, commonly hosted in DMZs.

Anyone that wants to communicate over the Internet must have at least one public IP address assigned to an interface on the network appliance that connects to the physical infrastructure connecting to the Internet. Recall that NAT is commonly used to translate private IP addresses to public IP addresses.

#### Using ipconfig

```powershell-session
ipconfig
```

We will notice some adapters, like the one in the output above, will have an IPv4 and an IPv6 address assigned in a [dual-stack configuration](https://www.cisco.com/c/dam/en_us/solutions/industries/docs/gov/IPV6at_a_glance_c45-625859.pdf) allowing resources to be reached over IPv4 or IPv6.

When network traffic is destined for an IP address located in a different network, the computer will send the traffic to its assigned `default gateway`. The default gateway is usually the IP address assigned to a NIC on an appliance acting as the router for a given LAN.

---
## Routing

#### Routing Table on Pwnbox

```shell-session
netstat -r
```

```shell-session
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         178.62.64.1     0.0.0.0         UG        0 0          0 eth0
10.10.10.0      10.10.14.1      255.255.254.0   UG        0 0          0 tun0
10.10.14.0      0.0.0.0         255.255.254.0   U         0 0          0 tun0
10.106.0.0      0.0.0.0         255.255.240.0   U         0 0          0 eth1
10.129.0.0      10.10.14.1      255.255.0.0     UG        0 0          0 tun0
178.62.64.0     0.0.0.0         255.255.192.0   U         0 0          0 eth0
```

Pwnbox is not using any routing protocols (EIGRP, OSPF, BGP, etc...) to learn each of those routes. It learned about those routes via its own directly connected interfaces (eth0, eth1, tun0). Stand-alone appliances designated as routers typically will learn routes using a combination of static route creation, dynamic routing protocols, and directly connected interfaces. Any traffic destined for networks not present in the routing table will be sent to the `default route`, which can also be referred to as the default gateway or gateway of last resort. 

When looking for opportunities to pivot, it can be helpful to look at the **hosts' routing table** to identify which networks we may be able to reach or which routes we may need to add.

---
## Protocols, Services & Ports

For further review of fundamental networking concepts, please reference the module [Introduction to Networking](https://academy.hackthebox.com/course/preview/introduction-to-networking).

 tools like [Draw.io](https://draw.io/) to build a visual of the network environment

