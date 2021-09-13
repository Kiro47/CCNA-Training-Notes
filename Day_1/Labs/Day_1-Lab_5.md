# Configure an Interface on a Cisco Router

## Configure
```sh
R1#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface Ethernet 0/0
R1(config-if)#ip address 10.10.1.1 255.255.255.0
R1(config-if)#description Link to SW2
R1(config-if)#no shutdown 
R1(config-if)#
*Sep 13 22:08:32.789: %LINK-3-UPDOWN: Interface Ethernet0/0, changed state to up
*Sep 13 22:08:33.793: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to up
```

## Verify:
```sh
R1(config-if)#do show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
C        10.10.1.0/24 is directly connected, Ethernet0/0
L        10.10.1.1/32 is directly connected, Ethernet0/0
C        10.10.3.0/24 is directly connected, Loopback0
L        10.10.3.1/32 is directly connected, Loopback0
```
Notice that we get two routes made:
* `10.10.1.0/24`
* `10.10.1.1/32`
We get this because it's a specification for the exact IP we entered (denoted by `L`).
Generally they can just kind of be ignored and are more informational than anything else.

## Try to ping
```sh
R1(config-if)#do ping 10.10.1.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.10, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 1/1/2 ms
R1(config-if)# end
R1#
*Sep 13 22:13:22.641: %SYS-5-CONFIG_I: Configured from console by console
```

## Verify interface config data
So we want to basically just look at what we have to verify it's exactly what we want and not just it works :tm:.

```sh
R1#show running-config interface ethernet 0/0
Building configuration...

Current configuration : 90 bytes
!
interface Ethernet0/0
 description Link to SW2
 ip address 10.10.1.1 255.255.255.0
end

R1#
R1#show ip interface brief
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.10.1.1       YES manual up                    up      
Ethernet0/1                unassigned      YES NVRAM  administratively down down    
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down    
Serial1/0                  unassigned      YES NVRAM  administratively down down    
Serial1/1                  unassigned      YES NVRAM  administratively down down    
Serial1/2                  unassigned      YES NVRAM  administratively down down    
Serial1/3                  unassigned      YES NVRAM  administratively down down    
Loopback0                  10.10.3.1       YES NVRAM  up                    up  
```
```sh
R1#show interface ethernet 0/0
Ethernet0/0 is up, line protocol is up 
  Hardware is AmdP2, address is aabb.cc00.2900 (bia aabb.cc00.2900)
  Description: Link to SW2
  Internet address is 10.10.1.1/24
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:01, output 00:00:04, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     274 packets input, 17822 bytes, 0 no buffer
     Received 229 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 input packets with dribble condition detected
     59 packets output, 6766 bytes, 0 underruns
     0 output errors, 0 collisions, 1 interface resets
     3 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
R1#
```