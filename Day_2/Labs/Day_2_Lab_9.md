# Troubleshooting Switch Media and Port Issues

## See that we can't ping from Switch 1 to PC2

```sh
SW1#ping 10.10.1.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.20, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

## Check the interface on Switch 2
```sh
SW2#show interface Ethernet0/2
Ethernet0/2 is administratively down, line protocol is down (disabled) 
  Hardware is AmdP2, address is aabb.cc00.7920 (bia aabb.cc00.7920)
  Description: Link to PC2
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Auto-duplex, Auto-speed, media type is unknown
  input flow-control is off, output flow-control is unsupported 
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input never, output 00:03:06, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/2000/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/0 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     0 packets input, 0 bytes, 0 no buffer
     Received 0 broadcasts (0 multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 input packets with dribble condition detected
     2 packets output, 154 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
```

Oh look, `administratively down`, guess I'll bring it back up?

```sh
SW2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#interface Ethernet 0/2
SW2(config-if)#no shutdown
SW2(config-if)#end
SW2#
*Sep 14 15:43:14.978: %SYS-5-CONFIG_I: Configured from console by console
SW2#
*Sep 14 15:43:16.308: %LINK-3-UPDOWN: Interface Ethernet0/2, changed state to up
SW2#
*Sep 14 15:43:18.317: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to up
SW2#
```

## Try to ping again
```sh
SW1#ping 10.10.1.20           
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.20, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
SW1#
```
Oh look it works neat.

## Save Switch 2's config to storage
```sh
SW2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 1015 bytes to 685 bytes[OK]
```