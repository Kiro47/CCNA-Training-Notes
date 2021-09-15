# Configuring IPv6 static routes

This entire lab could have been "just look at lab 12" and replace `ip` with `ipv6`.

## Verify nothing works
### PC 1
```sh
PC1#telnet 2001:db8:0:3::100
Trying 2001:DB8:0:3::100 ... 
% Connection timed out; remote host not responding

PC1#ping 2001:db8:0:3::100
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:3::100, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```
### Router 1
```sh
R1#show ipv6 route
IPv6 Routing Table - default - 7 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
C   2001:DB8:0:1::/64 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:0:1::1/128 [0/0]
     via Ethernet0/0, receive
C   2001:DB8:0:4::/64 [0/0]
     via Serial1/1, directly connected
L   2001:DB8:0:4::1/128 [0/0]
     via Serial1/1, receive
C   2001:DB8:0:5::/64 [0/0]
     via Serial1/2, directly connected
L   2001:DB8:0:5::1/128 [0/0]
     via Serial1/2, receive
L   FF00::/8 [0/0]
     via Null0, receive
R1#
```

### Router 2
```sh
R2#show ipv6 route
IPv6 Routing Table - default - 9 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
S   ::/0 [1/0]
     via 2001:DB8:0:6::2
S   2001:DB8:0:1::/64 [1/0]
     via 2001:DB8:0:5::1
C   2001:DB8:0:2::/64 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:0:2::1/128 [0/0]
     via Ethernet0/0, receive
C   2001:DB8:0:5::/64 [0/0]
     via Serial1/2, directly connected
L   2001:DB8:0:5::2/128 [0/0]
     via Serial1/2, receive
C   2001:DB8:0:6::/64 [0/0]
     via Serial1/3, directly connected
L   2001:DB8:0:6::1/128 [0/0]
     via Serial1/3, receive
L   FF00::/8 [0/0]
     via Null0, receive
R2#
```
### Router 3
```sh
R3#show ipv6 route
IPv6 Routing Table - default - 7 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
C   2001:DB8:0:3::/64 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:0:3::1/128 [0/0]
     via Ethernet0/0, receive
C   2001:DB8:0:4::/64 [0/0]
     via Serial1/1, directly connected
L   2001:DB8:0:4::2/128 [0/0]
     via Serial1/1, receive
C   2001:DB8:0:6::/64 [0/0]
     via Serial1/3, directly connected
L   2001:DB8:0:6::2/128 [0/0]
     via Serial1/3, receive
L   FF00::/8 [0/0]
     via Null0, receive
R3#
```

## Actually configure IPv6 unicast
### Router 1
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ipv6 unicast-routing
```

### Router 3
```sh
R3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#ipv6 unicast-routing
```

## Now add your routes
### Router 1
```sh
R1(config)#ipv6 route ::/0 2001:db8:0:4::2
R1(config)#ipv6 route 2001:db8:0:2::/64 2001:db8:0:5::2
```
#### Verify
```sh
R1(config)#do show ipv6 route
IPv6 Routing Table - default - 9 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
S   ::/0 [1/0]
     via 2001:DB8:0:4::2
C   2001:DB8:0:1::/64 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:0:1::1/128 [0/0]
     via Ethernet0/0, receive
S   2001:DB8:0:2::/64 [1/0]
     via 2001:DB8:0:5::2
C   2001:DB8:0:4::/64 [0/0]
     via Serial1/1, directly connected
L   2001:DB8:0:4::1/128 [0/0]
     via Serial1/1, receive
C   2001:DB8:0:5::/64 [0/0]
     via Serial1/2, directly connected
L   2001:DB8:0:5::1/128 [0/0]
     via Serial1/2, receive
L   FF00::/8 [0/0]
     via Null0, receive
R1(config)#
```
### PC 1 Verify
```sh
PC1#ping 2001:db8:0:2::100
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:2::100, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/15/41 ms
```
### Router 3 config
```sh
R3(config)#ipv6 route 2001:db8:0:1::/64 2001:db8:0:4::1
R3(config)#ipv6 route 2001:db8:0:2::/64 2001:db8:0:6::1
```
### Verify
```sh
R3(config)#do show ipv6 route static
IPv6 Routing Table - default - 9 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
S   2001:DB8:0:1::/64 [1/0]
     via 2001:DB8:0:4::1
S   2001:DB8:0:2::/64 [1/0]
     via 2001:DB8:0:6::1
```

## Try verifying on PC1 again
```sh
PC1#ping 2001:db8:0:3::100
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:0:3::100, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/15/42 ms
PC1#traceroute 2001:db8:0:3::100
Type escape sequence to abort.
Tracing the route to 2001:DB8:0:3::100

  1 2001:DB8:0:1::1 0 msec 1 msec 0 msec
  2 2001:DB8:0:4::2 9 msec 8 msec 9 msec
  3 2001:DB8:0:3::100 9 msec 9 msec 9 msec
PC1#
```

## Set Router 1 default route
```sh
R1(config)#ipv6 route ::/0 2001:db8:0:5::2 200
R1(config)#end
R1#
*Sep 14 21:34:33.676: %SYS-5-CONFIG_I: Configured from console by console
```
## Set Router 3 
```sh
R3(config)#ipv6 route 2001:db8:0:1::/64 2001:db8:0:6::1 200
```

## Take down Router 1 serial
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface serial1/1
R1(config-if)#shutdown
R1(config-if)#
*Sep 14 21:36:37.442: %LINK-5-CHANGED: Interface Serial1/1, changed state to administratively down
*Sep 14 21:36:38.442: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial1/1, changed state to down
R1(config-if)#
```
### Verify
#### Router 3
```sh
R3(config)#
*Sep 14 21:36:59.593: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial1/1, changed state to down
R3(config)#do show ipv6 route static
IPv6 Routing Table - default - 7 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
S   2001:DB8:0:1::/64 [200/0]
     via 2001:DB8:0:6::1
S   2001:DB8:0:2::/64 [1/0]
     via 2001:DB8:0:6::1
R3(config)#
```
Message shown, floating routes still alive.

#### Router 1
```sh
R1#
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface serial1/1
R1(config-if)#shutdown
R1(config-if)#
*Sep 14 21:36:37.442: %LINK-5-CHANGED: Interface Serial1/1, changed state to administratively down
*Sep 14 21:36:38.442: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial1/1, changed state to down
R1(config-if)#do show ipv6 route static 
IPv6 Routing Table - default - 7 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
S   ::/0 [200/0]
     via 2001:DB8:0:5::2
S   2001:DB8:0:2::/64 [1/0]
     via 2001:DB8:0:5::2
R1(config-if)#
```

## Check if we can telnet
```sh
PC1#telnet 2001:db8:0:3::100    
Trying 2001:DB8:0:3::100 ... Open


User Access Verification

Username: admin
Password: 
SRV1>
```
PRAISE BE

## Verify packets are taking from PC 1 to Server 1
```sh
PC1#traceroute 2001:db8:0:3::100
Type escape sequence to abort.
Tracing the route to 2001:DB8:0:3::100

  1 2001:DB8:0:1::1 1 msec 0 msec 0 msec
  2 2001:DB8:0:5::2 9 msec 9 msec 9 msec
  3 2001:DB8:0:6::2 17 msec 17 msec 18 msec
  4 2001:DB8:0:3::100 16 msec 18 msec 17 msec
PC1#
```

## Bring back Router 1's link back up for reasons
It's not like we disabled it to make life hell or anything.
```sh
R1(config-if)#no shutdown
R1(config-if)#
*Sep 14 21:40:17.505: %LINK-3-UPDOWN: Interface Serial1/1, changed state to up
*Sep 14 21:40:18.505: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial1/1, changed state to up
R1(config-if)#
```