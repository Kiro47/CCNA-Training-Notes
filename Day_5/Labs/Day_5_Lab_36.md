# Lab 36: Configure and Verify Port Security

## Determine MAC addresses of PCs

### PC1
```sh
PC1#show interface eth 0/0 | include addr
  Hardware is AmdP2, address is aabb.cc00.5900 (bia aabb.cc00.5900)
  Internet address is 10.10.1.10/24
PC1#
```

### PC2
```sh
PC2#show int eth0/0 | include addr
  Hardware is AmdP2, address is aabb.cc00.5a00 (bia aabb.cc00.5a00)
  Internet address is 10.10.1.20/24
PC2#
```

# Configure Switches
## Switch 1
```sh
SW1#show mac address-table 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    aabb.cc00.5900    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 1
SW1#
```

```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface eth0/1
SW1(config-if)#switchport mode access
SW1(config-if)#switchport port-security mac-addr aabb.cc00.5900
SW1(config-if)#switchport port-security
SW1(config-if)#end
SW1#
*Sep 17 19:52:17.156: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```

### Verify
```sh
SW1#show port-security
Secure Port  MaxSecureAddr  CurrentAddr  SecurityViolation  Security Action
                (Count)       (Count)          (Count)
---------------------------------------------------------------------------
      Et0/1              1            1                  0         Shutdown
---------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 4096
SW1#show port-security int eth0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : aabb.cc00.5900:1
Security Violation Count   : 0

SW1#
```

#### Change PC1's MAC and watch it disable
```sh
PC1#
PC1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#interface eth0/0
PC1(config-if)#mac-ad
PC1(config-if)#mac-address eeee.eeee.eee
PC1(config-if)#end
PC1#
*Sep 17 19:56:31.816: %SYS-5-CONFIG_I: Configured from console by console
PC1#
```
Notification on switch 1
```sh
SW1#
*Sep 17 19:56:29.872: %PM-4-ERR_DISABLE: psecure-violation error detected on Et0/1, putting Et0/1 in err-disable state
SW1#
*Sep 17 19:56:29.872: %PORT_SECURITY-2-PSECURE_VIOLATION: Security violation occurred, caused by MAC address eeee.eeee.0eee on port Ethernet0/1.
*Sep 17 19:56:30.875: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down
SW1#
*Sep 17 19:56:31.872: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to down
SW1#
```
```sh
SW1#show port-security int eth0/1
Port Security              : Enabled
Port Status                : Secure-shutdown
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : eeee.eeee.0eee:1
Security Violation Count   : 1

SW1#
```
```sh
SW1#show int eth0/1
Ethernet0/1 is down, line protocol is down (err-disabled) 
  Hardware is AmdP2, address is aabb.cc00.7510 (bia aabb.cc00.7510)
  Description: Link to PC1
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Auto-duplex, Auto-speed, media type is unknown
  input flow-control is off, output flow-control is unsupported 
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:09, output 00:01:12, output hang never
  Last clearing of "show interface" counters never
  Input queue: 5/2000/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/0 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     78 packets input, 8523 bytes, 0 no buffer
     Received 20 broadcasts (0 multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 input packets with dribble condition detected
     340 packets output, 26021 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
SW1#
```
#### Set PC1's mac address to original
```sh
PC1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#interface eth0/0
PC1(config-if)#no mac-addr
PC1(config-if)#end
PC1#
*Sep 17 19:58:11.758: %SYS-5-CONFIG_I: Configured from console by console
PC1#
```
##### See port is still shutdown
```sh
SW1#show port-security int eth0/1
Port Security              : Enabled
Port Status                : Secure-shutdown
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : eeee.eeee.0eee:1
Security Violation Count   : 1

SW1#
```
#### Bring port back up on Switch 1
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface eth0/1
SW1(config-if)#shutdown
SW1(config-if)#no shutdown
SW1(config-if)#end
SW1#
*Sep 17 19:59:19.459: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to up
SW1#
*Sep 17 19:59:19.911: %SYS-5-CONFIG_I: Configured from console by console
*Sep 17 19:59:20.459: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
SW1#
```

## Configure Sticky learning
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#int eth 0/1
SW1(config-if)#no switchport port-security
SW1(config-if)#no switchport port-security mac-addr aabb.cc00.5900
SW1(config-if)#switchport port-security maximum 2
SW1(config-if)#switchport port-security mac-address sticky
SW1(config-if)#switchport port-security
SW1(config-if)#end
SW1#
*Sep 17 20:00:53.391: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```

### Verify
```sh
SW1#show port-security
Secure Port  MaxSecureAddr  CurrentAddr  SecurityViolation  Security Action
                (Count)       (Count)          (Count)
---------------------------------------------------------------------------
      Et0/1              2            1                  0         Shutdown
---------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 4096
SW1#show port-security interface eth0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 1
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : aabb.cc00.5900:1
Security Violation Count   : 0

SW1#
```
```sh
SW1#show running-config int eth0/1
Building configuration...

Current configuration : 258 bytes
!
interface Ethernet0/1
 description Link to PC1
 switchport mode access
 switchport port-security maximum 2
 switchport port-security
 switchport port-security mac-address sticky
 switchport port-security mac-address sticky aabb.cc00.5900
 duplex auto
end

SW1#
```

### Save to startup
We want to save the MACs, otherwise it'll allow different ones on reboot.
```sh
SW1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 1145 bytes to 765 bytes[OK]
SW1#
```

### Change the MAC on PC1

We'll still have connection because it'll learn the new mac (maximum of 2 MACs)

```sh
PC1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#int eth0/0
PC1(config-if)#mac-addr eeee.eeee.eeee.eeee
                                      ^
% Invalid input detected at '^' marker.

PC1(config-if)#mac-addr eeee.eeee.eeee     
PC1(config-if)#do ping 10.10.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/201/1005 ms
PC1(config-if)#
```
#### Show on switch 1
```sh
SW1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 1145 bytes to 765 bytes[OK]
SW1#show port-security int eth0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 2
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 2
Last Source Address:Vlan   : eeee.eeee.eeee:1
Security Violation Count   : 0

SW1#
```
```sh
SW1#show port-security interface eth0/1 addr
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)    
----    -----------       ----                          -----   -------------
   1    aabb.cc00.5900    SecureSticky                  Et0/1        -
   1    eeee.eeee.eeee    SecureSticky                  Et0/1        -
-----------------------------------------------------------------------------
Total Addresses: 2

SW1#
```
```sh
SW1#show running-config int eth0/1
Building configuration...

Current configuration : 318 bytes
!
interface Ethernet0/1
 description Link to PC1
 switchport mode access
 switchport port-security maximum 2
 switchport port-security
 switchport port-security mac-address sticky
 switchport port-security mac-address sticky aabb.cc00.5900
 switchport port-security mac-address sticky eeee.eeee.eeee
 duplex auto
end

SW1#
```

### Change PC1's MAC again
This time we'll have no connection (because there's no more sticky slows open for MAC addresses)
```sh
PC1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#int eth0/0
PC1(config-if)#mac-addr cccc.cccc.cccc
PC1(config-if)#do ping 10.10.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.1, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
PC1(config-if)#
```
#### See Switch 1's status
```sh
SW1#
*Sep 17 20:10:40.732: %PM-4-ERR_DISABLE: psecure-violation error detected on Et0/1, putting Et0/1 in err-disable state
SW1#
*Sep 17 20:10:40.732: %PORT_SECURITY-2-PSECURE_VIOLATION: Security violation occurred, caused by MAC address cccc.cccc.cccc on port Ethernet0/1.
*Sep 17 20:10:41.734: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down
SW1#
*Sep 17 20:10:42.734: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to down
SW1#
```
```sh
SW1#show port-sec int eth0/1
Port Security              : Enabled
Port Status                : Secure-shutdown
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 2
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 2
Last Source Address:Vlan   : cccc.cccc.cccc:1
Security Violation Count   : 1

SW1#
```
```sh
SW1#show int eth0/1
Ethernet0/1 is down, line protocol is down (err-disabled) 
  Hardware is AmdP2, address is aabb.cc00.7510 (bia aabb.cc00.7510)
  Description: Link to PC1
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Auto-duplex, Auto-speed, media type is unknown
  input flow-control is off, output flow-control is unsupported 
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:09, output 00:01:11, output hang never
  Last clearing of "show interface" counters never
  Input queue: 5/2000/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/0 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     198 packets input, 20698 bytes, 0 no buffer
     Received 46 broadcasts (0 multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 input packets with dribble condition detected
     778 packets output, 59147 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
SW1#
```

## Configure Switch 1 to auto re-enable after violations
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#errdisable recovery cause psecure-violation
SW1(config)#errdisable recovery interval 30 
SW1(config)#end
SW1#
*Sep 17 20:13:11.193: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```
Sets port-security violations to re-enable after 30 seconds.

### Restore PC1's MAC again
```sh
PC1(config-if)#interface eth0/0
PC1(config-if)#no mac-addr
PC1(config-if)#end
PC1#
```

#### It eventually comes back up on Switch 1
```sh
SW1#
*Sep 17 20:18:03.780: %PM-4-ERR_RECOVER: Attempting to recover from psecure-violation err-disable state on Et0/1
SW1#
*Sep 17 20:18:05.784: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to up
*Sep 17 20:18:06.787: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
SW1#
```
##### Confirm on PC1
```sh
PC1#ping 10.10.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/201/1003 ms
PC1#
```

## Configure Switch 2

It's literally all the exact same thing, except setting different violation policies.

```sh
SW2# configure terminal  
Enter configuration commands, one per line. End with CNTL/Z.  
SW2(config)# interface Ethernet 0/2  
SW2(config-if)# switchport port-security violation restrict  
SW2(config-if)# end
```