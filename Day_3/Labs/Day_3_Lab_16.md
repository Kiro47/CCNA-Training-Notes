# Configure and Verify Single-Area OSPF

## Check configs

### Router 2
```sh
R2#show running-config | section ospf
 ip ospf network point-to-point
router ospf 1
 router-id 2.2.2.2
 network 10.0.1.0 0.0.0.255 area 0
 network 10.2.1.0 0.0.0.255 area 0
 network 10.10.12.0 0.0.0.255 area 0
R2#
```

### Router 3

```sh
R3#show ip protocols 
*** IP Routing is NSF aware ***

Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 3.3.3.3
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.1.1.0 0.0.0.255 area 0
    10.2.1.0 0.0.0.255 area 0
    10.10.13.0 0.0.0.255 area 0
  Routing Information Sources:
    Gateway         Distance      Last Update
    2.2.2.2              110      00:03:39
  Distance: (default is 110)

R3#
```
Above states routing protocol is ospf.

## Configure Router 1's RouterID

```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#router ospf 1
R1(config-router)#network 10.0.1.0 0.0.0.255 area 0
*Sep 15 15:33:30.828: %OSPF-5-ADJCHG: Process 1, Nbr 2.2.2.2 on Ethernet0/0 from LOADING to FULL, Loading Done
R1(config-router)#network 10.1.1.0 0.0.0.255 area 0  
*Sep 15 15:33:52.719: %OSPF-5-ADJCHG: Process 1, Nbr 3.3.3.3 on Ethernet0/1 from LOADING to FULL, Loading Done
R1(config-router)#network 10.10.11.0 0.0.0.255 area 0
R1(config-router)#end
R1#
*Sep 15 15:34:03.274: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

### Verify
```sh
R1#show ip ospf interface brief
Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C
Lo0          1     0               10.10.11.1/24      1     P2P   0/0
Et0/1        1     0               10.1.1.1/24        10    BDR   1/1
Et0/0        1     0               10.0.1.1/24        10    BDR   1/1
```
```sh
R1#show ip ospf interface
Loopback0 is up, line protocol is up 
  Internet Address 10.10.11.1/24, Area 0, Attached via Network Statement
  Process ID 1, Router ID 10.10.11.1, Network Type POINT_TO_POINT, Cost: 1
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           1         no          no            Base
  Transmit Delay is 1 sec, State POINT_TO_POINT
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 3/3, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 0, maximum is 0
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 0, Adjacent neighbor count is 0 
  Suppress hello for 0 neighbor(s)
Ethernet0/1 is up, line protocol is up 
  Internet Address 10.1.1.1/24, Area 0, Attached via Network Statement
  Process ID 1, Router ID 10.10.11.1, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State BDR, Priority 1
  Designated Router (ID) 3.3.3.3, Interface address 10.1.1.3
  Backup Designated router (ID) 10.10.11.1, Interface address 10.1.1.1
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:08
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 2/2, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1 
    Adjacent with neighbor 3.3.3.3  (Designated Router)
  Suppress hello for 0 neighbor(s)
Ethernet0/0 is up, line protocol is up 
  Internet Address 10.0.1.1/24, Area 0, Attached via Network Statement
  Process ID 1, Router ID 10.10.11.1, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State BDR, Priority 1
  Designated Router (ID) 2.2.2.2, Interface address 10.0.1.2
  Backup Designated router (ID) 10.10.11.1, Interface address 10.0.1.1
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:07
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1 
    Adjacent with neighbor 2.2.2.2  (Designated Router)
  Suppress hello for 0 neighbor(s)
R1#
```

```sh
R1#show ip protocols
*** IP Routing is NSF aware ***

Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 10.10.11.1
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.0.1.0 0.0.0.255 area 0
    10.1.1.0 0.0.0.255 area 0
    10.10.11.0 0.0.0.255 area 0
  Routing Information Sources:
    Gateway         Distance      Last Update
    2.2.2.2              110      00:02:23
    3.3.3.3              110      00:02:04
  Distance: (default is 110)

R1#
```
```sh
R1#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/DR         00:00:32    10.1.1.3        Ethernet0/1
2.2.2.2           1   FULL/DR         00:00:38    10.0.1.2        Ethernet0/0
R1#
```

```sh
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
C        10.0.1.0/24 is directly connected, Ethernet0/0
L        10.0.1.1/32 is directly connected, Ethernet0/0
C        10.1.1.0/24 is directly connected, Ethernet0/1
L        10.1.1.1/32 is directly connected, Ethernet0/1
O        10.2.1.0/24 [110/20] via 10.1.1.3, 00:03:26, Ethernet0/1
                     [110/20] via 10.0.1.2, 00:03:46, Ethernet0/0
C        10.10.11.0/24 is directly connected, Loopback0
L        10.10.11.1/32 is directly connected, Loopback0
O        10.10.12.0/24 [110/11] via 10.0.1.2, 00:03:46, Ethernet0/0
O        10.10.13.0/24 [110/11] via 10.1.1.3, 00:03:26, Ethernet0/1
R1#
```

## Verify on other Router
### Router 2
```sh
R2#
*Sep 15 15:33:30.827: %OSPF-5-ADJCHG: Process 1, Nbr 10.10.11.1 on Ethernet0/0 from LOADING to FULL, Loading Done
R2#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/DR         00:00:34    10.2.1.3        Ethernet0/2
10.10.11.1        1   FULL/BDR        00:00:34    10.0.1.1        Ethernet0/0
R2#
```


## Configure Interface cost on Router 1
```sh
nter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface eth0/0
R1(config-if)#ip ospf cost 1
R1(config-if)#end
R1#
*Sep 15 15:38:33.872: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

### Verify changes
```sh
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
C        10.0.1.0/24 is directly connected, Ethernet0/0
L        10.0.1.1/32 is directly connected, Ethernet0/0
C        10.1.1.0/24 is directly connected, Ethernet0/1
L        10.1.1.1/32 is directly connected, Ethernet0/1
O        10.2.1.0/24 [110/11] via 10.0.1.2, 00:00:17, Ethernet0/0
C        10.10.11.0/24 is directly connected, Loopback0
L        10.10.11.1/32 is directly connected, Loopback0
O        10.10.12.0/24 [110/2] via 10.0.1.2, 00:00:17, Ethernet0/0
O        10.10.13.0/24 [110/11] via 10.1.1.3, 00:05:02, Ethernet0/1
R1#
```

## Set Router 1 as passive
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#router ospf 1
R1(config-router)#passive-interface default
*Sep 15 15:39:38.432: %OSPF-5-ADJCHG: Process 1, Nbr 2.2.2.2 on Ethernet0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
*Sep 15 15:39:38.432: %OSPF-5-ADJCHG: Process 1, Nbr 3.3.3.3 on Ethernet0/1 from FULL to DOWN, Neighbor Down: Interface down or detached
R1(config-router)#no passive-interface eth0/1
R1(config-router)#end
R1#
*Sep 15 15:39:45.972: %OSPF-5-ADJCHG: Process 1, Nbr 3.3.3.3 on Ethernet0/1 from LOADING to FULL, Loading Done
*Sep 15 15:39:46.438: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

### Verify
```sh
R1#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/DR         00:00:34    10.1.1.3        Ethernet0/1
R1#
```
```sh
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
C        10.0.1.0/24 is directly connected, Ethernet0/0
L        10.0.1.1/32 is directly connected, Ethernet0/0
C        10.1.1.0/24 is directly connected, Ethernet0/1
L        10.1.1.1/32 is directly connected, Ethernet0/1
O        10.2.1.0/24 [110/20] via 10.1.1.3, 00:00:36, Ethernet0/1
C        10.10.11.0/24 is directly connected, Loopback0
L        10.10.11.1/32 is directly connected, Loopback0
O        10.10.12.0/24 [110/21] via 10.1.1.3, 00:00:36, Ethernet0/1
O        10.10.13.0/24 [110/11] via 10.1.1.3, 00:00:36, Ethernet0/1
R1#
```