# BGP 2 switch

## Table of Contents

- [Topology](#topology)
- [Usage](#usage)
  - [1. Download vEOS Image](#1-download-veos-image)
  - [2. Create Virtual Environment](#2-create-virtual-environment)
  - [3. Delete Virtual Environment](#3-delete-virtual-environment)
  - [4. Show Configurations](#4-show-configurations)
  - [5. Send ICMP Packet between Servers](#5-send-icmp-packet-between-servers)

## Topology

This is the simplest BGP sandbox environment.  
There are 2 switches and 2 servers.  
The switches distribute each directly connected route to the another switch using BGP.

<div align="center">
  <img src="https://github.com/um7a/network_sandbox/raw/main/bgp_2switch/veos/docs/topology.png" width="100%">
</div>

## Usage

### 1. Download vEOS Image
Download `vEOS-lab-<version>-virtualbox.box` file. This is available from Arista [Software Downloads](https://www.arista.com/en/support/software-download).  
The following commands will add the new box.  
```bash
$ vagrant box add --name vEOS-lab-4.19.10M ./vEOS-lab-4.19.10M-virtualbox.box

$ vagrant box list
...
vEOS-lab-4.19.10M (virtualbox, 0)
```

### 2. Create Virtual Environment

```
$ vagrant up
```

### 3. Delete Virtual Environment

```
$ vagrant destroy
```

### 4. Show Configurations

Login to the `switch01` or `switch02`.

```bash
$ vagrant ssh switch01  # (or switch02)
```

Show running config.
```
switch01#show running-config
! Command: show running-config
! device: switch01 (vEOS, EOS-4.19.10M)
!
! boot system flash:/vEOS-lab.swi
!
event-handler dhclient
   trigger on-boot
   action bash sudo /mnt/flash/initialize_ma1.sh
!
transceiver qsfp default-mode 4x10G
!
spanning-tree mode mstp
!
aaa authorization exec default local
!
aaa root secret sha512 ...
!
username admin privilege 15 role network-admin secret sha512 ...
username vagrant privilege 15 role network-admin secret sha512 ...
!
interface Ethernet1
   no switchport
   ip address 10.0.102.0/31
!
interface Ethernet2
   no switchport
   ip address 10.0.101.1/24
!
interface Loopback0
   ip address 10.0.104.1/32
!
interface Management1
   ip address 10.0.2.15/24
!
ip routing
!
ip prefix-list NW1 seq 10 permit 10.0.101.0/24
!
route-map ALLOW_NW1 permit 10
   match ip address prefix-list NW1
!
router bgp 4200000000
   router-id 10.0.104.1
   neighbor UPLINK peer-group
   neighbor UPLINK timers 1 3
   neighbor UPLINK maximum-routes 12000
   neighbor 10.0.102.1 peer-group UPLINK
   neighbor 10.0.102.1 remote-as 4200000001
   neighbor 10.0.102.1 route-map ALLOW_NW1 out
   redistribute connected route-map ALLOW_NW1
!
management api http-commands
   no shutdown
!
end
```

Show route.

```
-bash-4.3# FastCli
switch01>show ip route

VRF name: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B I - iBGP, B E - eBGP,
       R - RIP, I L1 - ISIS level 1, I L2 - ISIS level 2,
       O3 - OSPFv3, A B - BGP Aggregate, A O - OSPF Summary,
       NG - Nexthop Group Static Route, V - VXLAN Control Service

Gateway of last resort is not set

 C      10.0.2.0/24 is directly connected, Management1
 C      10.0.101.0/24 is directly connected, Ethernet2
 C      10.0.102.0/31 is directly connected, Ethernet1
 B E    10.0.103.0/24 [200/0] via 10.0.102.1, Ethernet1
 C      10.0.104.1/32 is directly connected, Loopback0

```

Show BGP summary.

```
-bash-4.3# FastCli
switch01>show ip bgp summary
BGP summary information for VRF default
Router identifier 10.0.104.1, local AS number 4200000000
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State  PfxRcd PfxAcc
  10.0.102.1       4  4200000001      1772      1772    0    0 00:29:27 Estab  1      1
```

### 5. Send ICMP Packet between Servers

```bash
$ vagrant ssh server01

$ ping 10.0.103.2 -c 3
PING 10.0.103.2 (10.0.103.2) 56(84) bytes of data.
64 bytes from 10.0.103.2: icmp_seq=1 ttl=62 time=38.0 ms
64 bytes from 10.0.103.2: icmp_seq=2 ttl=62 time=40.1 ms
64 bytes from 10.0.103.2: icmp_seq=3 ttl=62 time=45.7 ms

--- 10.0.103.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 38.010/41.267/45.679/3.235 ms
```
