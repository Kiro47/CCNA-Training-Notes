# Configure Basic IPv6 Connectivity

## Enable IPv6 Routing

### Router 1
```sh

R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ipv6 unicast-routing 
R1(config)#interface Serial1/2
R1(config-if)#ipv6 address 2001:db8:0:5::1/64
R1(config-if)#do ping 2001:db8:0:5::2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:5::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 7/8/9 ms
R1(config-if)#                       
R1(config-if)#interface Ethernet0/0
R1(config-if)#ipv6 addr 2001:db8:0:1::1/64
R1(config-if)#interface Serial1/1
R1(config-if)#ipv6 addr 2001:db8:0:4::1/64
R1(config-if)#end
R1#
*Sep 14 18:56:12.184: %SYS-5-CONFIG_I: Configured from console by console
R1#              
```
#### Show config
```sh
R1#show ipv6 interface Et0/0
Ethernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::A8BB:CCFF:FE00:6600 
  No Virtual link-local address(es):
  Description: Link to SW1
  Global unicast address(es):
    2001:DB8:0:1::1, subnet is 2001:DB8:0:1::/64 
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
    FF02::1:FF00:6600
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds (using 30000)
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
  ```
### Router 3

```sh
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#ipv6 unicast-routing
R3(config)#interface Serial1/1
R3(config-if)#ipv6 address 2001:db8:0:4::2/64
R3(config-if)#do ping 2001:db8:0:4::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:4::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/8/9 ms
R3(config-if)#
R3(config-if)#interface Serial1/3
R3(config-if)#ipv6 addr 2001:db8:0:6::2/64
R3(config-if)#do ping 2001:db8:0:6::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:6::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/8/9 ms
R3(config-if)#interface Eth0/0 
R3(config-if)#ipv6 addr 2001:db8:0:3::1/64
R3(config-if)#end
R3#
*Sep 14 19:00:50.160: %SYS-5-CONFIG_I: Configured from console by console
R3#
```

####  Show config

```sh
R3#show interface Eth0/0 | include addr
  Hardware is AmdP2, address is aabb.cc00.8a00 (bia aabb.cc00.8a00)
  Internet address is 10.10.3.1/24
```

## Enable autoconfig

### Server 1
```sh
SRV1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SRV1(config)#interface Eth0/0
SRV1(config-if)#ipv6 addr autoconfig default
SRV1(config-if)#end
SRV1#
*Sep 14 19:03:16.193: %SYS-5-CONFIG_I: Configured from console by console
SRV1#show ipv6 interface brief Eth0/0
Ethernet0/0            [up/up]
    FE80::A8BB:CCFF:FE00:9F00
    2001:DB8:0:3:A8BB:CCFF:FE00:9F00
SRV1#
SRV1#show ipv6 route
IPv6 Routing Table - default - 4 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
ND  ::/0 [2/0]
     via FE80::A8BB:CCFF:FE00:8A00, Ethernet0/0
NDp 2001:DB8:0:3::/64 [2/0]
     via Ethernet0/0, directly connected
L   2001:DB8:0:3:A8BB:CCFF:FE00:9F00/128 [0/0]
     via Ethernet0/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
SRV1#
```

#### Ping Verification
```sh
SRV1#ping 2001:db8:0:3::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:3::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/9 ms
SRV1#ping 2001:db8:0:4::2 
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:4::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
SRV1#ping 2001:db8:0:6::2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:6::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
SRV1#
```

#### Verify cannot ping R1/R2
```sh
SRV1#ping 2001:db8:0:4::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:4::1, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

### PC 1

```sh
PC1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#interface Eth0/0
PC1(config-if)#ipv6 addr autoconfig default
PC1(config-if)#end
PC1#
*Sep 14 19:05:53.577: %SYS-5-CONFIG_I: Configured from console by console
```

#### Verify
```sh
PC1#show ipv6 interface brief eth0/0
Ethernet0/0            [up/up]
    FE80::A8BB:CCFF:FE00:9A00
    2001:DB8:0:1:A8BB:CCFF:FE00:9A00
PC1#show ipv6 route
IPv6 Routing Table - default - 4 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
ND  ::/0 [2/0]
     via FE80::A8BB:CCFF:FE00:6600, Ethernet0/0
NDp 2001:DB8:0:1::/64 [2/0]
     via Ethernet0/0, directly connected
L   2001:DB8:0:1:A8BB:CCFF:FE00:9A00/128 [0/0]
     via Ethernet0/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
PC1#
```
##### Ping verification

```sh
PC1#ping 2001:db8:0:1::1 
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:1::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/4/17 ms
PC1#ping 2001:db8:0:4::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:4::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
PC1#ping 2001:db8:0:5::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:5::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
PC1#
```