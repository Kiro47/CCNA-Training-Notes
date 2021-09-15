# Configure a Router on a Stick

## Include a Router interface in a VLAN
```sh
SW1#show interfaces status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0     Trunk to SW2       connected    trunk        auto   auto unknown
Et0/1     Link to R1 VLAN 1  connected    1            auto   auto unknown
Et0/2     Link to R1 VLAN 2  connected    1            auto   auto unknown
Et0/3                        connected    1            auto   auto unknown
Et1/0     Link to PC1        connected    1            auto   auto unknown
Et1/1     Link to PC2        connected    2            auto   auto unknown
Et1/2                        connected    1            auto   auto unknown
Et1/3                        connected    1            auto   auto unknown
SW1#
```
## Configure SW 1
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface eth0/2
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 2
SW1(config-if)#end
SW1#
*Sep 14 23:01:46.983: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```

## Configure router 1
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface eth0/1
R1(config-if)#ip addr 10.10.2.1 255.255.255.0 
R1(config-if)#no shutdown
R1(config-if)#end
R1#
*Sep 14 23:05:39.326: %SYS-5-CONFIG_I: Configured from console by console
R1#
*Sep 14 23:05:39.331: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to up
*Sep 14 23:05:40.336: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
R1#
```

## Check Switch 2 vlans
```sh
SW2#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/1, Et0/2, Et0/3, Et1/0
                                                Et1/2, Et1/3
2    Engineering                      active    Et1/1
256  NoHosts                          active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
SW2#
```

## Check PC4's connections
```sh
PC4#show cdp neighbors detail
-------------------------
Device ID: SW2
Entry address(es): 
  IP address: 10.10.1.5
Platform: Linux Unix,  Capabilities: Switch IGMP 
Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet1/1
Holdtime : 144 sec

Version :
Cisco IOS Software, Solaris Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Experimental Version 15.1(20130919:231344) [dstivers-sept19-2013pm-team_track 107]
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Thu 19-Sep-13 22:38 by dstivers

advertisement version: 2
VTP Management Domain: ''
Native VLAN: 2
Duplex: half

PC4#
```
VLAN 2 (also why is this at a half duplex?)

### Fix PC4's IP
```sh
PC4#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC4(config)#ip default-gateway 10.10.2.1
PC4(config)#end
PC4#
*Sep 14 23:07:43.326: %SYS-5-CONFIG_I: Configured from console by console
PC4#
```

### Now check PC4 can access VLAN 1
```sh
PC4#ping 10.10.1.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.10, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC4#
```

#### Still is on VLAN 2
```sh
PC4#show cdp neighbors detail
-------------------------
Device ID: SW2
Entry address(es): 
  IP address: 10.10.1.5
Platform: Linux Unix,  Capabilities: Switch IGMP 
Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet1/1
Holdtime : 158 sec

Version :
Cisco IOS Software, Solaris Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Experimental Version 15.1(20130919:231344) [dstivers-sept19-2013pm-team_track 107]
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Thu 19-Sep-13 22:38 by dstivers

advertisement version: 2
VTP Management Domain: ''
Native VLAN: 2
Duplex: half

PC4#
```
## Configure PC2
### VLAN check
```sh
PC2#show cdp neighbors detail
-------------------------
Device ID: SW1
Entry address(es): 
  IP address: 10.10.1.4
Platform: Linux Unix,  Capabilities: Switch IGMP 
Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet1/1
Holdtime : 162 sec

Version :
Cisco IOS Software, Solaris Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Experimental Version 15.1(20130919:231344) [dstivers-sept19-2013pm-team_track 107]
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Thu 19-Sep-13 22:38 by dstivers

advertisement version: 2
VTP Management Domain: ''
Native VLAN: 2
Duplex: half

PC2#
```
Native VLAN 2

### Configure
```sh
PC2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC2(config)#ip default-gateway 10.10.2.1
PC2(config)#end
PC2#
*Sep 14 23:10:39.167: %SYS-5-CONFIG_I: Configured from console by console
PC2#
```
#### Verify
```sh
*Sep 14 23:10:39.167: %SYS-5-CONFIG_I: Configured from console by console
PC2#ping 10.10.1.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.30, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC2#
```

## Configure Trunk on Switch 1
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface eth0/2
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 2
SW1(config-if)#end
SW1#
*Sep 14 23:01:46.983: %SYS-5-CONFIG_I: Configured from console by console
SW1#         
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#vlan 3
SW1(config-vlan)#name Marketing
SW1(config-vlan)#exit
SW1(config)#interface eth1/0
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 3
SW1(config-if)#end
SW1#
*Sep 14 23:12:27.183: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```

### Verify
```sh
SW1#show vlan id 3

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
3    Marketing                        active    Et0/0, Et1/0

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
3    enet  100003     1500  -      -      -        -    -        0      0   

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------

```

## Configure PC 1 to be on VLAN 3
```sh
PC1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#interface eth0/0
PC1(config-if)#ip addr 10.10.3.10 255.255.255.0
PC1(config-if)#exit
PC1(config)#ip default-gateway 10.10.3.1
PC1(config)#end
PC1#
*Sep 14 23:14:05.511: %SYS-5-CONFIG_I: Configured from console by console
PC1#
```

## Configure Switch 2 with VLAN 3
```sh
SW2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#vlan 3 
SW2(config-vlan)#name Marketing
SW2(config-vlan)#exit
SW2(config)#interface eth1/0
SW2(config-if)#switchport mode access
SW2(config-if)#switchport access vlan 3
SW2(config-if)#end
SW2#
*Sep 14 23:14:54.969: %SYS-5-CONFIG_I: Configured from console by console
SW2#
```

### Verify
```sh
SW2#show interfaces Eth1/0 status

Port      Name               Status       Vlan       Duplex  Speed Type
Et1/0     Link to PC3        connected    3            auto   auto unknown
SW2#show vlan id 3

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
3    Marketing                        active    Et0/0, Et1/0

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
3    enet  100003     1500  -      -      -        -    -        0      0   

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------

SW2#
```


## Configure PC 3 to be on VLAN 3
```sh
PC3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC3(config)#interface eth0/0
PC3(config-if)#ip addr 10.10.3.30 255.255.255.0
PC3(config-if)#exit
PC3(config)#ip default-gateway 10.10.3.1
PC3(config)#end
PC3#
*Sep 14 23:18:06.071: %SYS-5-CONFIG_I: Configured from console by console
PC3#
```

### Verify
```sh
PC3#ping 10.10.3.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.10, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/2 ms
PC3#
```

## Trunk VLAN 2 and 3 on Switch1

```sh
SW1#
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface eth0/2
SW1(config-if)#switchport trunk encapsulation dot1q
SW1(config-if)#switchport trunk native vlan 256
SW1(config-if)#switchport trunk allowed vlan 2,3
SW1(config-if)#switchport mode trunk
SW1(config-if)#end
SW1#
*Sep 14 23:20:48.480: %SYS-5-CONFIG_I: Configured from console by console
*Sep 14 23:20:49.004: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to down
SW1#
*Sep 14 23:20:52.004: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to up
SW1#
```

## Configure Router 1 with trunk
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface eth0/1
R1(config-if)#no ip addr
R1(config-if)#interface eth 0/1.2
R1(config-subif)#encapsulation dot1q 2
R1(config-subif)#ip addr 10.10.2.1 255.255.255.0
R1(config)#interface eth0/1.3
R1(config-subif)#encapsulation dot1q 3 
R1(config-subif)#ip addr 10.10.3.1 255.255.255.0    
R1(config-subif)#end
R1#
*Sep 14 23:22:22.657: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

## Verify configs

### Router 1
```sh
R1#ping 10.10.3.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.10, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 1/1/1 ms
R1#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
R1#ping 10.10.3.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.3.30, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 1/1/1 ms
R1#ping 10.10.2.40
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.40, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
R1#               
```
### PC 1
```sh
PC1#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
PC1#traceroute 10.10.2.20
Type escape sequence to abort.
Tracing the route to 10.10.2.20
VRF info: (vrf in name/id, vrf out name/id)
  1 10.10.3.1 0 msec 0 msec 1 msec
  2 10.10.2.20 0 msec *  2 msec
PC1#ping 10.10.1.4
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.4, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#ping 10.10.1.5
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.5, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#
```
