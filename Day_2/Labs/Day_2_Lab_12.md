# Configure and verify IPv4 Static Routes

## Verify Device Reachability
```sh
PC1#ping 10.10.1.4
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.4, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#ping 10.10.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/202/1006 ms
PC1#
PC1#show ip route
Default gateway is 10.10.1.1

Host               Gateway           Last Use    Total Uses  Interface
ICMP redirect cache is empty
PC1#ping 10.1.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
PC1#ping 10.1.1.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
PC1#ping 10.1.1.9
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.9, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
PC1#
```
These ICMP packets do make it, but the issue is R1 doesn't have a route back to respond with.

## Configure Static Route
### Router 1
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip route 10.10.2.0 255.255.255.0 10.1.1.9 
R1(config)#ip route 10.10.4.0 255.255.255.0 10.1.1.1
R1(config)#end
R1#
*Sep 14 20:54:11.021: %SYS-5-CONFIG_I: Configured from console by console
R1#
```
### Router 2
```sh
R2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ip route 10.10.1.0 255.255.255.0 10.1.1.10
R2(config)#ip route 10.10.3.0 255.255.255.0 10.1.1.5 
R2(config)#
```

### Verify from PC 1
```sh
PC1#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 8/9/11 ms
PC1#ping 10.10.3.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.30, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
PC1#ping 10.1.1.6
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.6, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
PC1#
```
Ping fails because the subnet `10.1.1.4/30` is the point-to-point link between Router 2 and Router 3, Router 1 has no access to it.

## Configure access
### Router 1
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip route 10.1.1.4 255.255.255.252 10.1.1.9
R1(config)#end
R1#
*Sep 14 20:58:20.637: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

### Router 2
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip route 10.1.1.4 255.255.252 10.1.1.9
                                        ^
% Invalid input detected at '^' marker.

R1(config)#ip route 10.1.1.4 255.255.255.252 10.1.1.9
R1(config)#end
R1#
*Sep 14 20:58:20.637: %SYS-5-CONFIG_I: Configured from console by console
R1#
```
### Router 3
```sh
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#ip route 10.10.1.0 255.255.255.0 10.1.1.2
R3(config)#ip route 10.10.2.0 255.255.255.0 10.1.1.6
R3(config)#ip route 10.1.1.8 255.255.255.252 10.1.1.2 
R3(config)#end
R3#
*Sep 14 21:00:46.373: %SYS-5-CONFIG_I: Configured from console by console
R3#
```


## Verify
### Router 1
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

      10.0.0.0/8 is variably subnetted, 9 subnets, 3 masks
C        10.1.1.0/30 is directly connected, Serial1/1
L        10.1.1.2/32 is directly connected, Serial1/1
S        10.1.1.4/30 [1/0] via 10.1.1.9
C        10.1.1.8/30 is directly connected, Serial1/2
L        10.1.1.10/32 is directly connected, Serial1/2
C        10.10.1.0/24 is directly connected, Ethernet0/0
L        10.10.1.1/32 is directly connected, Ethernet0/0
S        10.10.2.0/24 [1/0] via 10.1.1.9
S        10.10.4.0/24 [1/0] via 10.1.1.1
R1#
```
### PC 1
```sh
PC1#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/8/9 ms
PC1#ping 10.10.2.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.30, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
PC1#ping 10.1.1.6  
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.6, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 9/9/10 ms
PC1#ping 10.1.1.5
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.5, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 13/13/14 ms
PC1#
```
I seem to have made a typo somewhere here as `10.10.2.30` should respond

## Traceroute
### PC 1
```sh
PC1#traceroute 10.10.2.20
Type escape sequence to abort.
Tracing the route to 10.10.2.20
VRF info: (vrf in name/id, vrf out name/id)
  1 10.10.1.1 1 msec 0 msec 1 msec
  2 10.1.1.9 9 msec 9 msec 9 msec
  3 10.10.2.20 10 msec *  8 msec
PC1#traceroute 10.10.2.30
Type escape sequence to abort.
Tracing the route to 10.10.2.30
VRF info: (vrf in name/id, vrf out name/id)
  1 10.10.1.1 0 msec 1 msec 0 msec
  2 10.1.1.9 9 msec 9 msec 9 msec
  3  *  *  * 
  4  *  *  * 
  5  *  *  * 
  6  *  *  * 
  7  *  *  * 
  8  *  *  * 
  9  *  *  *
  10 *  *  *
```

```sh
R1#show ip interface brief
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.10.1.1       YES NVRAM  up                    up      
Ethernet0/1                unassigned      YES NVRAM  administratively down down    
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down    
Serial1/0                  unassigned      YES NVRAM  administratively down down    
Serial1/1                  10.1.1.2        YES NVRAM  up                    up      
Serial1/2                  10.1.1.10       YES NVRAM  up                    up      
Serial1/3                  unassigned      YES NVRAM  administratively down down    
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

      10.0.0.0/8 is variably subnetted, 9 subnets, 3 masks
C        10.1.1.0/30 is directly connected, Serial1/1
L        10.1.1.2/32 is directly connected, Serial1/1
S        10.1.1.4/30 [1/0] via 10.1.1.9
C        10.1.1.8/30 is directly connected, Serial1/2
L        10.1.1.10/32 is directly connected, Serial1/2
C        10.10.1.0/24 is directly connected, Ethernet0/0
L        10.10.1.1/32 is directly connected, Ethernet0/0
S        10.10.2.0/24 [1/0] via 10.1.1.9
S        10.10.4.0/24 [1/0] via 10.1.1.1
R1#
```

## Repair interface
```sh
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#interface serial 1/1
R3(config-if)#no shutdown
R3(config-if)#end
R3#
*Sep 14 21:05:49.822: %SYS-5-CONFIG_I: Configured from console by console
R3#
```

### Router 1
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip route 10.10.2.0 255.255.255.0 10.1.1.1 2
R1(config)#ip route 10.10.3.0 255.255.255.0 10.1.1.9 2
R1(config)#ip route 10.1.1.4 255.255.255.252 10.1.1.1 2 
R1(config)#end
R1#
*Sep 14 21:07:45.255: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

#### Verify
```sh
R1#show running-config | include route
ip route 10.1.1.4 255.255.255.252 10.1.1.9
ip route 10.1.1.4 255.255.255.252 10.1.1.1 2
ip route 10.10.2.0 255.255.255.0 10.1.1.9
ip route 10.10.2.0 255.255.255.0 10.1.1.1 2
ip route 10.10.3.0 255.255.255.0 10.1.1.9 2
ip route 10.10.4.0 255.255.255.0 10.1.1.1
R1#
R1#show ip route static
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 10 subnets, 3 masks
S        10.1.1.4/30 [1/0] via 10.1.1.9
S        10.10.2.0/24 [1/0] via 10.1.1.9
S        10.10.3.0/24 [2/0] via 10.1.1.9
S        10.10.4.0/24 [1/0] via 10.1.1.1
R1#
```

### Router 3
```sh
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#ip route 10.10.1.0 255.255.255.0 10.1.1.6 2
R3(config)#ip route 10.10.2.0 255.255.255.0 10.1.1.2 2
R3(config)#ip route 10.1.1.8 255.255.255.252 10.1.1.6 2 
R3(config)#end
R3#
*Sep 14 21:09:57.119: %SYS-5-CONFIG_I: Configured from console by console
R3#
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#interface S1/1
R3(config-if)#shutdown
R3(config-if)#end
R3#
*Sep 14 21:10:28.448: %SYS-5-CONFIG_I: Configured from console by console
R3#
*Sep 14 21:10:29.920: %LINK-5-CHANGED: Interface Serial1/1, changed state to administratively down
*Sep 14 21:10:30.921: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial1/1, changed state to down
R3#
```
#### Verify on Router 1
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

      10.0.0.0/8 is variably subnetted, 10 subnets, 3 masks
C        10.1.1.0/30 is directly connected, Serial1/1
L        10.1.1.2/32 is directly connected, Serial1/1
S        10.1.1.4/30 [1/0] via 10.1.1.9
C        10.1.1.8/30 is directly connected, Serial1/2
L        10.1.1.10/32 is directly connected, Serial1/2
C        10.10.1.0/24 is directly connected, Ethernet0/0
L        10.10.1.1/32 is directly connected, Ethernet0/0
S        10.10.2.0/24 [1/0] via 10.1.1.9
S        10.10.3.0/24 [2/0] via 10.1.1.9
S        10.10.4.0/24 [1/0] via 10.1.1.1
R1#
```

#### Verify on PC 1
```sh
PC1#ping 10.10.3.30      
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.30, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 14/16/18 ms
PC1#traceroute 10.10.3.30
Type escape sequence to abort.
Tracing the route to 10.10.3.30
VRF info: (vrf in name/id, vrf out name/id)
  1 10.10.1.1 0 msec 0 msec 0 msec
  2 10.1.1.9 9 msec 9 msec 9 msec
  3 10.1.1.5 17 msec 17 msec 17 msec
  4 10.10.3.30 17 msec *  17 msec
PC1#
```
### Router 3
```sh
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#interface Serial 1/1
R3(config-if)#no shutdown
R3(config-if)#end
R3#
*Sep 14 21:12:16.375: %SYS-5-CONFIG_I: Configured from console by console
R3#
*Sep 14 21:12:17.815: %LINK-3-UPDOWN: Interface Serial1/1, changed state to up
*Sep 14 21:12:18.820: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial1/1, changed state to up
R3#
```

## Configure and verify default route

### Router 1
```sh
R1#
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#no ip route 10.1.1.4 255.255.255.252 10.1.1.9
R1(config)#no ip route 10.1.1.4 255.255.255.252 10.1.1.1 2
R1(config)#no ip route 10.10.2.0 255.255.255.0 10.1.1.9   
R1(config)#no ip route 10.10.2.0 255.255.255.0 10.1.1.1 2
R1(config)#no ip route 10.10.3.0 255.255.255.0 10.1.1.1  
%No matching route to delete
R1(config)#no ip route 10.10.3.0 255.255.255.0 10.1.1.9 2
R1(config)# end
```
Hey look, I found my earlier typo (forgotten command route added)

```sh
R1(config)#no ip route 10.10.4.0 255.255.255.0 10.1.1.1
``` 
Maybe it was a typo, had to do the above to delete an extra route.

```sh
R1#show running-config | include route
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

      10.0.0.0/8 is variably subnetted, 6 subnets, 3 masks
C        10.1.1.0/30 is directly connected, Serial1/1
L        10.1.1.2/32 is directly connected, Serial1/1
C        10.1.1.8/30 is directly connected, Serial1/2
L        10.1.1.10/32 is directly connected, Serial1/2
C        10.10.1.0/24 is directly connected, Ethernet0/0
L        10.10.1.1/32 is directly connected, Ethernet0/0
R1#
```

### Verify from PC 1

```sh
PC1#ping 10.10.3.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.30, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
PC1#ping 10.10.3.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.20, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
PC1#ping 10.1.1.9  
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.9, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/8/10 ms
PC1#traceroute 10.10.2.20
Type escape sequence to abort.
Tracing the route to 10.10.2.20
VRF info: (vrf in name/id, vrf out name/id)
  1 10.10.1.1 0 msec 1 msec 1 msec
  2 10.10.1.1 !H  *  !H 
PC1#          
```