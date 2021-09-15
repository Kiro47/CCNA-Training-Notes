# Configuring VLANs and Trunks

## Check connectivity from PC 1
```sh
PC1#ping 10.10.1.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.20, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#ping 10.10.1.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.30, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#ping 10.10.1.40
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.40, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
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

## Configure PC 2 and change it's address
```sh

PC2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC2(config)#interface Eth0/0
PC2(config-if)#ip addr 10.10.2.20 255.255.255.0
PC2(config-if)#end
PC2#
*Sep 14 21:44:45.431: %SYS-5-CONFIG_I: Configured from console by console
PC2#
```


## Configure PC 4 and change it's address
```sh
PC4#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
PC4(config)#interface eth0/0
PC4(config-if)#ip addr 10.10.2.40 255.255.255.0
PC4(config-if)#end
PC4#
*Sep 14 21:45:23.159: %SYS-5-CONFIG_I: Configured from console by console
PC4#
```

## Verify PC4 and PC2 can communicate
They're both on the new subnet, so should be able to.
```sh
PC4#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC4#
```

## Check Switch 1's config
```sh
SW1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/1, Et0/2, Et0/3, Et1/0
                                                Et1/1, Et1/2, Et1/3
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0   
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
```
## Configure Switch 1
```sh
SW1(config)#vlan 2 
SW1(config-vlan)#name Engineering
SW1(config-vlan)#end
SW1#
*Sep 14 21:47:39.489: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```
### Verify
```sh
SW1#show vlan brief   

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/1, Et0/2, Et0/3, Et1/0
                                                Et1/1, Et1/2, Et1/3
2    Engineering                      active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
SW1#
SW1#show vlan id 2

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
2    Engineering                      active    Et0/0

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
2    enet  100002     1500  -      -      -        -    -        0      0   

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------

SW1#
```

## Force Switches to use IEE802.1Q instead of ISL

### Switch 1
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface eth0/0
SW1(config-if)#switchport trunk encapsulation dot1q
SW1(config-if)#end
SW1#
*Sep 14 21:49:38.007: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```
### Switch 2
```sh
SW2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#interface eth0/0
SW2(config-if)#switchport trunk encapsulation dot1q
SW2(config-if)#end
SW2#
*Sep 14 21:50:17.928: %SYS-5-CONFIG_I: Configured from console by console
SW2#
```

## Check Switch 1 and add new Null vlan
```sh
SW1#show interface Eth0/0 switchport
Name: Et0/0
Switchport: Enabled
Administrative Mode: dynamic desirable
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 1 (default)
Administrative Native VLAN tagging: enabled
Voice VLAN: none
Administrative private-vlan host-association: none 
Administrative private-vlan mapping: none 
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk Native VLAN tagging: enabled
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk associations: none
Administrative private-vlan trunk mappings: none
Operational private-vlan: none
Trunking VLANs Enabled: ALL
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
          
Appliance trust: none
SW1#
```
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#vlan 256
SW1(config-vlan)#name NoHosts
SW1(config-vlan)#exit
SW1(config)#
```
```sh
SW1(config)#interface eth0/0
SW1(config-if)#switchport trunk native vlan 256
SW1(config-if)#switchport mode trunk
SW1(config-if)#end
SW1#
*Sep 14 21:53:39.216: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```
## Set Switch 2 to be synchronized
```sh
SW2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#vlan 2
SW2(config-vlan)#name Engineering
SW2(config-vlan)#vlan 256
SW2(config-vlan)#name NoHo
*Sep 14 21:54:17.043: %CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on Ethernet0/0 (1), with SW1 Ethernet0/0 (256).
SW2(config-vlan)#name NoHosts
SW2(config-vlan)#exit
SW2(config)#interface eth0/0
SW2(config-if)#switchport trunk native vlan 256
SW2(config-if)#switchport mode trunk
SW2(config-if)#end
SW2#
*Sep 14 21:54:45.896: %SYS-5-CONFIG_I: Configured from console by console
SW2#
```
### Verify
```sh
SW2#show interface eth0/0 switchport
Name: Et0/0
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 256 (NoHosts)
Administrative Native VLAN tagging: enabled
Voice VLAN: none
Administrative private-vlan host-association: none 
Administrative private-vlan mapping: none 
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk Native VLAN tagging: enabled
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk associations: none
Administrative private-vlan trunk mappings: none
Operational private-vlan: none
Trunking VLANs Enabled: ALL
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
          
Appliance trust: none
SW2#
```

## Configure Switch 2 to use VLAN 2
```sh
SW2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#interface eth1/1
SW2(config-if)#switchport access vlan 2
SW2(config-if)#switchport mode access
SW2(config-if)#end
```

### Verify
```sh
SW2#show interface eth1/1 switchport
Name: Et1/1
Switchport: Enabled
Administrative Mode: static access
Operational Mode: static access
Administrative Trunking Encapsulation: negotiate
Operational Trunking Encapsulation: native
Negotiation of Trunking: Off
Access Mode VLAN: 2 (Engineering)
Trunking Native Mode VLAN: 1 (default)
Administrative Native VLAN tagging: enabled
Voice VLAN: none
Administrative private-vlan host-association: none 
Administrative private-vlan mapping: none 
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk Native VLAN tagging: enabled
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk associations: none
Administrative private-vlan trunk mappings: none
Operational private-vlan: none
Trunking VLANs Enabled: ALL
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
          
Appliance trust: none
SW2#
```
```sh
SW2#show interface status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0     Link to SW1        connected    trunk        auto   auto unknown
Et0/1                        connected    1            auto   auto unknown
Et0/2                        connected    1            auto   auto unknown
Et0/3                        connected    1            auto   auto unknown
Et1/0     Link to PC3        connected    1            auto   auto unknown
Et1/1     Link to PC4        connected    2            auto   auto unknown
Et1/2                        connected    1            auto   auto unknown
Et1/3                        connected    1            auto   auto unknown
SW2#
```
## Verify on Switch 1
```sh
SW1#
*Sep 14 21:54:17.053: %CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on Ethernet0/0 (256), with SW2 Ethernet0/0 (1).
SW1#show interface status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0     Link to SW2        connected    trunk        auto   auto unknown
Et0/1                        connected    1            auto   auto unknown
Et0/2                        connected    1            auto   auto unknown
Et0/3                        connected    1            auto   auto unknown
Et1/0     Link to PC1        connected    1            auto   auto unknown
Et1/1     Link to PC2        connected    1            auto   auto unknown
Et1/2                        connected    1            auto   auto unknown
Et1/3                        connected    1            auto   auto unknown
SW1#
```
##  Check Connectivity on PC4

```sh
PC4#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
PC4#
```
It fails as expected

## Configure Switch 1 eth1/1 to be VLAN 2
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface eth1/1
SW1(config-if)#switchport access vlan 2
SW1(config-if)#switchport mode access
SW1(config-if)#end
SW1#
*Sep 14 21:58:38.809: %SYS-5-CONFIG_I: Configured from console by console
SW1#
```
### Verify
```sh
SW1#show interface eth1/1 switchport
Name: Et1/1
Switchport: Enabled
Administrative Mode: static access
Operational Mode: static access
Administrative Trunking Encapsulation: negotiate
Operational Trunking Encapsulation: native
Negotiation of Trunking: Off
Access Mode VLAN: 2 (Engineering)
Trunking Native Mode VLAN: 1 (default)
Administrative Native VLAN tagging: enabled
Voice VLAN: none
Administrative private-vlan host-association: none 
Administrative private-vlan mapping: none 
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk Native VLAN tagging: enabled
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk associations: none
Administrative private-vlan trunk mappings: none
Operational private-vlan: none
Trunking VLANs Enabled: ALL
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
          
Appliance trust: none
SW1#
```

```sh
SW1#show interface status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0     Link to SW2        connected    trunk        auto   auto unknown
Et0/1                        connected    1            auto   auto unknown
Et0/2                        connected    1            auto   auto unknown
Et0/3                        connected    1            auto   auto unknown
Et1/0     Link to PC1        connected    1            auto   auto unknown
Et1/1     Link to PC2        connected    2            auto   auto unknown
Et1/2                        connected    1            auto   auto unknown
Et1/3                        connected    1            auto   auto unknown
SW1#
```

## Check PC4 for connectivity again
```sh
PC4#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
PC4#
```
It works as expected for `10.10.2.20`, however should now fail on `10.10.1.10`.

```sh
PC4#ping 10.10.1.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.10, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
PC4#
```
Which it does, meaning it was all a success.
