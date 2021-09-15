# Explore Packet Forwarding

## Verify connectivity from PC 1 to SRV 1

```sh
PC1#ping 10.10.3.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.30, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#traceroute 10.10.3.30
Type escape sequence to abort.
Tracing the route to 10.10.3.30
VRF info: (vrf in name/id, vrf out name/id)
  1 10.10.1.1 1 msec 1 msec 1 msec
  2 10.1.1.1 1 msec 1 msec 1 msec
  3 10.10.3.30 1 msec *  2 msec
PC1#
```

## Get info of devices in path

### PC 1

```sh
PC1#show interfaces Ethernet 0/0 | include  add
  Hardware is AmdP2, address is aabb.cc00.9100 (bia aabb.cc00.9100)
  Internet address is 10.10.1.10/24
```

### Router 1

```sh
R1#show interfaces e0/0 | include addr 
  Hardware is AmdP2, address is aabb.cc00.7300 (bia aabb.cc00.7300)
  Internet address is 10.10.1.1/24
R1#show interfaces e0/1 | include addr
  Hardware is AmdP2, address is aabb.cc00.7310 (bia aabb.cc00.7310)
  Internet address is 10.1.1.2/30
R1#
```

### Router 3

```sh
R3#show interface e0/0 | include addr
  Hardware is AmdP2, address is aabb.cc00.7600 (bia aabb.cc00.7600)
  Internet address is 10.10.3.1/24
R3#show interface e0/1 | include addr
  Hardware is AmdP2, address is aabb.cc00.7610 (bia aabb.cc00.7610)
  Internet address is 10.1.1.1/30
```

### Server 1
```sh
SRV1#show interface e0/0 | include addr
  Hardware is AmdP2, address is aabb.cc00.9300 (bia aabb.cc00.9300)
  Internet address is 10.10.3.30/24
```
## Show ARP table

### PC 1
```sh
PC1#show ip arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.1.1               4   aabb.cc00.7300  ARPA   Ethernet0/0
Internet  10.10.1.10              -   aabb.cc00.9100  ARPA   Ethernet0/0
```

## Debug ARP

### PC 1
```
PC1#debug arp
ARP packet debugging is on
```
Bring down interface to clear ARP
```sh
PC1#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#interface e0/0
PC1(config-if)#do show ip arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.1.1               5   aabb.cc00.7300  ARPA   Ethernet0/0
Internet  10.10.1.10              -   aabb.cc00.9100  ARPA   Ethernet0/0
PC1(config-if)#shutdown
PC1(config-if)#
*Sep 14 14:28:29.158: %LINK-5-CHANGED: Interface Ethernet0/0, changed state to administratively down
*Sep 14 14:28:30.162: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to down
PC1(config-if)#do show ip arp
PC1(config-if)#

```

#### Bring back up get regen ARP table
```sh
PC1(config-if)#no shutdown
PC1(config-if)#
*Sep 14 14:29:00.446: IP ARP: sent rep src 10.10.1.10 aabb.cc00.9100,
                 dst 10.10.1.10 ffff.ffff.ffff Ethernet0/0
*Sep 14 14:29:00.446: IP ARP: sent rep src 10.10.1.10 aabb.cc00.9100,
                 dst 10.10.1.10 ffff.ffff.ffff Ethernet0/0
PC1(config-if)#
*Sep 14 14:29:02.437: %LINK-3-UPDOWN: Interface Ethernet0/0, changed state to up
*Sep 14 14:29:03.441: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to up
PC1(config-if)# end
```

#### Show ARP again with ping
```sh
*Sep 14 14:29:56.645: %SYS-5-CONFIG_I: Configured from console by console
PC1#ping 10.10.3.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.30, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/201/1002 ms
PC1#
*Sep 14 14:30:20.465: IP ARP: creating incomplete entry for IP address: 10.10.1.1 interface Ethernet0/0
*Sep 14 14:30:20.465: IP ARP: sent req src 10.10.1.10 aabb.cc00.9100,
                 dst 10.10.1.1 0000.0000.0000 Ethernet0/0
*Sep 14 14:30:20.466: IP ARP: rcvd rep src 10.10.1.1 aabb.cc00.7300, dst 10.10.1.10 Ethernet0/0
PC1#
```

#### Turn off debugging

```sh
PC1#undebug all
All possible debugging has been turned off
PC1#undebug  arp
ARP packet debugging is off
```

## Look at Switch's "ARP"

### Delete cache
```sh
SW1#clear mac add      
SW1#show mac address-table 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    aabb.cc00.7300    DYNAMIC     Et0/0
   1    aabb.cc00.9100    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2
SW1#clear mac address-table dynamic 
```

### Regens in 10 seconds from ARP broadcast
```sh
SW1#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    aabb.cc00.7300    DYNAMIC     Et0/0
   1    aabb.cc00.9100    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2
```

### Can see keepalive time from interface

```sh
SW1#show interface e0/0
Ethernet0/0 is up, line protocol is up (connected) 
  Hardware is AmdP2, address is aabb.cc00.9400 (bia aabb.cc00.9400)
  Description: Link to R1
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Auto-duplex, Auto-speed, media type is unknown
  input flow-control is off, output flow-control is unsupported 
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:06, output 00:00:01, output hang never
  Last clearing of "show interface" counters never
  Input queue: 4/2000/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/0 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     128 packets input, 13732 bytes, 0 no buffer
     Received 23 broadcasts (0 multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 input packets with dribble condition detected
     622 packets output, 46173 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
SW1#
```