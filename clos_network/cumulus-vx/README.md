# clos_network

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Topology](#topology)
- [Usage](#usage)
  - [Create Virtual Environment](#create-virtual-environment)
  - [Delete Virtual Environment](#delete-virtual-environment)
  - [Show Configuration](#show-configuration)
  - [Show Route](#show-route)
  - [Send ICMP Packet between Servers](#send-icmp-packet-between-servers)

## Topology

This is a clos network sandbox environment.

There are 2 pods in this network. Each pods are interconnected across the super spines. Each pod has 2 leaf and 2 spine switches.

<div align="center">
  <img src="https://github.com/um7a/network_sandbox/raw/main/clos_network/cumulus-vx/docs/topology_hostname.png" width="80%">
</div>

Each node has the following interfaces and IP Addresses.

<div align="center">
  <img src="https://github.com/um7a/network_sandbox/raw/main/clos_network/cumulus-vx/docs/topology_ip_address.png" width="80%">
</div>

Each switch has the following AS Number.

<div align="center">
  <img src="https://github.com/um7a/network_sandbox/raw/main/clos_network/cumulus-vx/docs/topology_as_number.png" width="80%">
</div>

## Usage

### Create Virtual Environment

```bash
$ vagrant up
```

### Delete Virtual Environment

```bash
$ vagrant destroy
```

### Show Configuration

```bash
vagrant@leaf01:mgmt:~$ sudo net show configuration
```

### Show Route

leaf01

```bash
vagrant@leaf01:mgmt:~$ sudo net show route
show ip route
=============
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR, f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
B>* 0.0.0.0/0 [20/0] via fe80::a00:27ff:fe47:f93, swp2, weight 1, 00:36:33
  *                  via fe80::a00:27ff:febf:3048, swp1, weight 1, 00:36:33
C>* 10.0.101.1/32 is directly connected, lo, 00:59:47
C>* 10.0.102.0/24 is directly connected, vlan1, 00:59:28



show ipv6 route
===============
Codes: K - kernel route, C - connected, S - static, R - RIPng,
       O - OSPFv3, I - IS-IS, B - BGP, N - NHRP, T - Table,
       v - VNC, V - VNC-Direct, A - Babel, D - SHARP, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C * fe80::/64 is directly connected, vlan1, 00:59:27
C * fe80::/64 is directly connected, swp2, 00:59:36
C>* fe80::/64 is directly connected, swp1, 00:59:41
```

spine01

```bash
vagrant@spine01:mgmt:~$ sudo net show route
show ip route
=============
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR, f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
B>* 0.0.0.0/0 [20/0] via fe80::a00:27ff:fe67:2e6f, swp2, weight 1, 00:46:08
  *                  via fe80::a00:27ff:fec4:4f16, swp1, weight 1, 00:46:08
C>* 10.0.101.3/32 is directly connected, lo, 01:02:49
B>* 10.0.102.0/24 [20/0] via fe80::a00:27ff:feb5:b12a, swp3, weight 1, 01:02:22
B>* 10.0.103.0/24 [20/0] via fe80::a00:27ff:fe8c:c0f1, swp4, weight 1, 01:02:16



show ipv6 route
===============
Codes: K - kernel route, C - connected, S - static, R - RIPng,
       O - OSPFv3, I - IS-IS, B - BGP, N - NHRP, T - Table,
       v - VNC, V - VNC-Direct, A - Babel, D - SHARP, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C * fe80::/64 is directly connected, swp4, 01:02:24
C * fe80::/64 is directly connected, swp3, 01:02:30
C * fe80::/64 is directly connected, swp2, 01:02:36
C>* fe80::/64 is directly connected, swp1, 01:02:42
```

super-spine01

```bash
vagrant@super-spine01:mgmt:~$ sudo net show route
show ip route
=============
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR, f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C>* 10.0.101.9/32 is directly connected, lo, 00:53:13
B>* 10.0.102.0/24 [20/0] via fe80::a00:27ff:fe34:e934, swp1, weight 1, 00:53:02
B>* 10.0.103.0/24 [20/0] via fe80::a00:27ff:fe34:e934, swp1, weight 1, 00:53:02
B>* 10.0.104.0/24 [20/0] via fe80::a00:27ff:feca:e062, swp2, weight 1, 00:52:56
B>* 10.0.105.0/24 [20/0] via fe80::a00:27ff:feca:e062, swp2, weight 1, 00:52:56



show ipv6 route
===============
Codes: K - kernel route, C - connected, S - static, R - RIPng,
       O - OSPFv3, I - IS-IS, B - BGP, N - NHRP, T - Table,
       v - VNC, V - VNC-Direct, A - Babel, D - SHARP, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C * fe80::/64 is directly connected, swp2, 00:52:58
C>* fe80::/64 is directly connected, swp1, 00:53:05
```

### Send ICMP Packet between Servers

Send ICMP Packet between servers in the same pod (server01 -> server02).

```bash
$ vagrant.exe ssh server01

vagrant@server01:~$ ping 10.0.103.2 -c 3
PING 10.0.103.2 (10.0.103.2) 56(84) bytes of data.
64 bytes from 10.0.103.2: icmp_seq=1 ttl=61 time=7.78 ms
64 bytes from 10.0.103.2: icmp_seq=2 ttl=61 time=4.91 ms
64 bytes from 10.0.103.2: icmp_seq=3 ttl=61 time=5.21 ms

--- 10.0.103.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 4.908/5.966/7.780/1.288 ms
```

Send ICMP Packet between servers in the different pods (server01 -> server04).

```bash
$ vagrant.exe ssh server01

vagrant@server01:~$ ping 10.0.105.2 -c 3
PING 10.0.105.2 (10.0.105.2) 56(84) bytes of data.
64 bytes from 10.0.105.2: icmp_seq=1 ttl=59 time=8.98 ms
64 bytes from 10.0.105.2: icmp_seq=2 ttl=59 time=7.55 ms
64 bytes from 10.0.105.2: icmp_seq=3 ttl=59 time=7.88 ms

--- 10.0.105.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2006ms
rtt min/avg/max/mdev = 7.554/8.140/8.982/0.610 ms
```
