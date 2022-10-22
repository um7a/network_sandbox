# OSPF 2 switch

## Table of Contents

- [Topology](#topology)
- [Usage](#usage)
  - [1. Download vEOS Image](#1-download-veos-image)
  - [2. Create Virtual Environment](#2-create-virtual-environment)
  - [3. Delete Virtual Environment](#3-delete-virtual-environment)
  - [4. Show Configurations](#4-show-configurations)
  - [5. Send ICMP Packet between Servers](#5-send-icmp-packet-between-servers)

## Topology

This is the simplest OSPF sandbox environment.  
There are 2 switches and 2 servers.  
The switches distribute each directly connected route to the another switch using OSPF.

<div align="center">
  <img src="https://github.com/um7a/network_sandbox/raw/main/ospf_2switch/veos/docs/topology.png" width="100%">
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
-bash-4.3# FastCli
switch01>enable
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
   ip address 10.0.102.1/30
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   ip address 10.0.101.1/24
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
router ospf 10
   router-id 10.0.102.1
   redistribute connected route-map ALLOW_NW1
   max-lsa 12000
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
 C      10.0.102.0/30 is directly connected, Ethernet1
 O E2   10.0.103.0/24 [110/1] via 10.0.102.2, Ethernet1

```

Show OSPF neigbor.

```
-bash-4.3# FastCli
switch01>show ip ospf neighbor
Neighbor ID     VRF      Pri State                  Dead Time   Address         Interface
10.0.102.2      default  1   FULL/BDR               00:00:34    10.0.102.2      Ethernet1
```

### 5. Send ICMP Packet between Servers

```bash
$ vagrant ssh server01

$ ping 10.0.103.2 -c 3
PING 10.0.103.2 (10.0.103.2) 56(84) bytes of data.
64 bytes from 10.0.103.2: icmp_seq=1 ttl=62 time=57.9 ms
64 bytes from 10.0.103.2: icmp_seq=2 ttl=62 time=39.5 ms
64 bytes from 10.0.103.2: icmp_seq=3 ttl=62 time=45.0 ms

--- 10.0.103.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 39.456/47.447/57.896/7.725 ms
```
