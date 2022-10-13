# BGP 2 switch

## Table of Contents

- [Topology](#topology)
- [Usage](#usage)
  - [1. Create Virtual Environment](#1-create-virtual-environment)
  - [2. Delete Virtual Environment](#2-delete-virtual-environment)
  - [3. Show Configurations](#3-show-configurations)
  - [4. Send ICMP Packet between Servers](#4-send-icmp-packet-between-servers)

## Topology

This is the simplest BGP sandbox environment.  
There are 2 switches and 2 servers.  
And the switches distribute each directly connected route to the another switch using BGP.

<div align="center">
  <img src="https://github.com/um7a/network_sandbox/raw/main/bgp_2switch/docs/topology.png" width="100%">
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
vagrant@switch01:mgmt:~$ sudo net show route
show ip route
=============
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR, f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C>* 10.0.101.0/24 is directly connected, swp2, 00:07:28
C>* 10.0.102.1/32 is directly connected, lo, 00:07:29
B>* 10.0.103.0/24 [20/0] via fe80::a00:27ff:feaf:6114, swp1, weight 1, 00:02:49



show ipv6 route
===============
Codes: K - kernel route, C - connected, S - static, R - RIPng,
       O - OSPFv3, I - IS-IS, B - BGP, N - NHRP, T - Table,
       v - VNC, V - VNC-Direct, A - Babel, D - SHARP, F - PBR,
       f - OpenFabric,
       > - selected route, * - FIB route, q - queued, r - rejected, b - backup
       t - trapped, o - offload failure
C * fe80::/64 is directly connected, swp1, 00:07:20
C>* fe80::/64 is directly connected, swp2, 00:07:28
```

Show BGP neigbor.  
`BGP state = Established` means BGP peering session is successfully established between `switch01` and `switch02`.
```bash
$ sudo net show bgp neighbor
BGP neighbor on swp1: fe80::a00:27ff:fe99:67d5, remote AS 4200000001, local AS 4200000000, external link
Hostname: switch02
 Member of peer-group UPLINK for session parameters
  BGP version 4, remote router ID 10.0.102.2, local router ID 10.0.102.1
  BGP state = Established, up for 00:14:06
  Last read 00:00:00, Last write 00:00:00
  Hold time is 3, keepalive interval is 1 seconds
  Configured hold time is 3, keepalive interval is 1 seconds
  Neighbor capabilities:
    4 Byte AS: advertised and received
    AddPath:
      IPv4 Unicast: RX advertised IPv4 Unicast and received
    Extended nexthop: advertised and received
      Address families by peer:
                   IPv4 Unicast
    Route refresh: advertised and received(old & new)
    Address Family IPv4 Unicast: advertised and received
    Hostname Capability: advertised (name: switch01,domain name: n/a) received (name: switch02,domain name: n/a)
    Graceful Restart Capability: advertised and received
      Remote Restart timer is 120 seconds
      Address families by peer:
        none
  Graceful restart information:
    End-of-RIB send: IPv4 Unicast
    End-of-RIB received: IPv4 Unicast
    Local GR Mode: Helper*
    Remote GR Mode: Helper
    R bit: False
    Timers:
      Configured Restart Time(sec): 120
      Received Restart Time(sec): 120
    IPv4 Unicast:
      F bit: False
      End-of-RIB sent: Yes
      End-of-RIB sent after update: Yes
      End-of-RIB received: Yes
      Timers:
        Configured Stale Path Time(sec): 360
  Message statistics:
    Inq depth is 0
    Outq depth is 0
                         Sent       Rcvd
    Opens:                  2          1
    Notifications:          0          0
    Updates:                4          4
    Keepalives:           845        845
    Route Refresh:          0          0
    Capability:             0          0
    Total:                851        850
  Minimum time between advertisement runs is 0 seconds

 For address family: IPv4 Unicast
  UPLINK peer-group member
  Update group 1, subgroup 1
  Packet Queue length 0
  Community attribute sent to this neighbor(all)
  2 accepted prefixes

  Connections established 1; dropped 0
  Last reset 00:15:59,  No AFI/SAFI activated for peer
Local host: fe80::a00:27ff:fedc:e1b2, Local port: 179
Foreign host: fe80::a00:27ff:fe99:67d5, Foreign port: 58436
Nexthop: 10.0.102.1
Nexthop global: fe80::a00:27ff:fedc:e1b2
Nexthop local: fe80::a00:27ff:fedc:e1b2
BGP connection: shared network
BGP Connect Retry Timer in Seconds: 10
Read thread: on  Write thread: on  FD used: 24
```

### 4. Send ICMP Packet between Servers

```bash
$ vagrant ssh server01

$ ping 10.0.103.2 -c 3  # (ip address of server02)
PING 10.0.103.2 (10.0.103.2) 56(84) bytes of data.
64 bytes from 10.0.103.2: icmp_seq=1 ttl=62 time=5.62 ms
64 bytes from 10.0.103.2: icmp_seq=2 ttl=62 time=5.09 ms
64 bytes from 10.0.103.2: icmp_seq=3 ttl=62 time=4.85 ms

--- 10.0.103.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2008ms
rtt min/avg/max/mdev = 4.852/5.188/5.622/0.321 ms
```
