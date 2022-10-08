# OSPF 2 switch

## Table of Contents

- [Topology](#topology)
- [Usage](#usage)
  - [1. Create Virtual Environment](#1-create-virtual-environment)
  - [2. Delete Virtual Environment](#2-delete-virtual-environment)
  - [3. Show Configurations](#3-show-configurations)
  - [4. Send ICMP Packet between Servers](#4-send-icmp-packet-between-servers)

## Topology

This is the simplest OSPF sandbox environment.  
There are 2 switches and 2 servers.  
And the switches distribute each directly connected route to the another switch using OSPF.

<div align="center">
  <img src="https://github.com/um7a/network_sandbox/raw/main/ospf_2switch/docs/topology.png" width="100%">
</div>

## Usage

### 1. Create Virtual Environment

```
$ vagrant up
```

### 2. Delete Virtual Environment

```
$ vagrant destroy
```

### 3. Show Configurations

Login to the `switch01` or `switch02`.

```bash
$ vagrant ssh switch01  # (or switch02)
```

Show route.

```bash
$ sudo net show route
show ip route
=============
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR, f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C>* 10.0.101.0/24 is directly connected, swp2, 00:07:08
O   10.0.102.0/30 [110/10] is directly connected, swp1, weight 1, 00:07:08
C>* 10.0.102.0/30 is directly connected, swp1, 00:07:08
O>* 10.0.103.0/24 [110/20] via 10.0.102.2, swp1, weight 1, 00:05:07



show ipv6 route
===============
Codes: K - kernel route, C - connected, S - static, R - RIPng,
       O - OSPFv3, I - IS-IS, B - BGP, N - NHRP, T - Table,
       v - VNC, V - VNC-Direct, A - Babel, D - SHARP, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C * fe80::/64 is directly connected, swp2, 00:07:06
C>* fe80::/64 is directly connected, swp1, 00:07:06
```

Show OSPF neigbor.

```bash
$ sudo net show ospf neighbor
Neighbor ID     Pri State           Dead Time Address         Interface                        RXmtL RqstL DBsmL
10.0.102.2        1 Full/Backup       37.488s 10.0.102.2      swp1:10.0.102.1                      0     0     0
```

### 4. Send ICMP Packet between Servers

```bash
$ vagrant ssh server01

$ ping 10.0.103.2  # (ip address of server02)
PING 10.0.103.2 (10.0.103.2) 56(84) bytes of data.
64 bytes from 10.0.103.2: icmp_seq=1 ttl=62 time=5.32 ms
64 bytes from 10.0.103.2: icmp_seq=2 ttl=62 time=5.04 ms
64 bytes from 10.0.103.2: icmp_seq=3 ttl=62 time=5.05 ms
^C
--- 10.0.103.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2115ms
rtt min/avg/max/mdev = 5.036/5.137/5.322/0.130 ms
```
