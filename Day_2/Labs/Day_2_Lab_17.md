# Configure and Verify EtherChannel

## Check Switch 1's config

```sh
SW1#show interfaces status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0     Link to SW4        connected    trunk        auto   auto unknown
Et0/1     Link to SW4        connected    trunk        auto   auto unknown
Et0/2     Link to SW3        connected    trunk        auto   auto unknown
Et0/3     Link to SW3        connected    trunk        auto   auto unknown
Et1/0     Link to PC1        connected    10           auto   auto unknown
Et1/1                        disabled     1            auto   auto unknown
Et1/2                        disabled     1            auto   auto unknown
Et1/3                        disabled     1            auto   auto unknown
SW1#show spanning-tree vlan 10

VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     aabb.cc00.8200
             Cost        100
             Port        3 (Ethernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32778  (priority 32768 sys-id-ext 10)
             Address     aabb.cc00.7200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 100       128.1    Shr 
Et0/1               Altn BLK 100       128.2    Shr 
Et0/2               Root FWD 100       128.3    Shr 
Et0/3               Altn BLK 100       128.4    Shr 
Et1/0               Desg FWD 100       128.5    Shr 


SW1#
```

## Check Switch 3's config

```sh

SW3#show spanning-tree vlan 10

VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     aabb.cc00.8200
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    24586  (priority 24576 sys-id-ext 10)
             Address     aabb.cc00.8200
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr 
Et0/1               Desg FWD 100       128.2    Shr 
Et0/2               Desg FWD 100       128.3    Shr 
Et0/3               Desg FWD 100       128.4    Shr 
Et1/0               Desg FWD 100       128.5    Shr 
Et1/2               Desg FWD 100       128.7    Shr 
Et1/3               Desg FWD 100       128.8    Shr 

          
SW3#
```

## Configure Switch 1 to Switch 3 EtherChannel

### Disable Switch 1 links

```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#interface range Eth0/2 -3 
SW1(config-if-range)#shutdown
SW1(config-if-range)#
*Sep 15 02:49:11.727: %LINK-5-CHANGED: Interface Ethernet0/2, changed state to administratively down
*Sep 15 02:49:11.727: %LINK-5-CHANGED: Interface Ethernet0/3, changed state to administratively down
*Sep 15 02:49:12.732: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to down
*Sep 15 02:49:12.732: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/3, changed state to down
SW1(config-if-range)#
```

### Disable Switch 3 links
```sh
SW3#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW3(config)#interface range eth0/2 - 3
SW3(config-if-range)#shutdown
SW3(config-if-range)#
*Sep 15 02:50:17.566: %LINK-5-CHANGED: Interface Ethernet0/2, changed state to administratively down
*Sep 15 02:50:17.566: %LINK-5-CHANGED: Interface Ethernet0/3, changed state to administratively down
*Sep 15 02:50:18.573: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to down
*Sep 15 02:50:18.573: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/3, changed state to down
SW3(config-if-range)#
```

### Create channel-group Switch 1
```sh
SW1(config-if-range)#channel-group 1 mode active
Creating a port-channel interface Port-channel 1
```

### Create channel-group Swithc 3
```sh
SW3(config-if-range)#channel-group  1 mode active
Creating a port-channel interface Port-channel 1
```

### Bring Links up

#### Switch 1
```sh
SW1(config-if-range)#no shutdown
SW1(config-if-range)#
*Sep 15 02:51:42.528: %LINK-3-UPDOWN: Interface Ethernet0/2, changed state to up
*Sep 15 02:51:42.528: %LINK-3-UPDOWN: Interface Ethernet0/3, changed state to up
SW1(config-if-range)#
*Sep 15 02:51:51.649: %EC-5-L3DONTBNDL2: Et0/3 suspended: LACP currently not enabled on the remote port.
*Sep 15 02:51:52.311: %EC-5-L3DONTBNDL2: Et0/2 suspended: LACP currently not enabled on the remote port.
```

#### Switch 3
```sh
SW3(config-if-range)#no shutdown 
SW3(config-if-range)#
*Sep 15 02:54:24.870: %LINK-3-UPDOWN: Interface Ethernet0/2, changed state to up
*Sep 15 02:54:24.870: %LINK-3-UPDOWN: Interface Ethernet0/3, changed state to up
*Sep 15 02:54:25.874: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to up
*Sep 15 02:54:25.874: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/3, changed state to up
SW3(config-if-range)#
*Sep 15 02:54:30.829: %LINEPROTO-5-UPDOWN: Line protocol on Interface Port-channel1, changed state to up
SW3(config-if-range)#
```

### Set descriptions
#### Switch 1
```sh
SW1(config)#interface port-channel 1
SW1(config-if)#description EChannel to SW3
SW1(config-if)#end
SW1#
*Sep 15 02:55:08.980: %SYS-5-CONFIG_I: Configured from console by console

```
#### Switch 3
```sh
SW3(config-if-range)#exit
SW3(config)#interface port-channel 1
SW3(config-if)#description EChannel to SW1
SW3(config-if)#
```

### Check configs to verify

#### Switch 1
```sh
SW1#show interface status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0     Link to SW4        connected    trunk        auto   auto unknown
Et0/1     Link to SW4        connected    trunk        auto   auto unknown
Et0/2     Link to SW3        connected    trunk        auto   auto unknown
Et0/3     Link to SW3        connected    trunk        auto   auto unknown
Et1/0     Link to PC1        connected    10           auto   auto unknown
Et1/1                        disabled     1            auto   auto unknown
Et1/2                        disabled     1            auto   auto unknown
Et1/3                        disabled     1            auto   auto unknown
Po1       EChannel to SW3    connected    trunk        auto   auto 
SW1#show spanning-tree vlan 10

VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     aabb.cc00.2000
             Cost        56
             Port        65 (Port-channel1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32778  (priority 32768 sys-id-ext 10)
             Address     aabb.cc00.1400
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr 
Et0/1               Desg FWD 100       128.2    Shr 
Et1/0               Desg FWD 100       128.5    Shr 
Po1                 Root FWD 56        128.65   Shr 


SW1#
```

#### Switch 3
```sh
SW3#show interface status

Port      Name               Status       Vlan       Duplex  Speed Type
Et0/0     Link to SW2        connected    trunk        auto   auto unknown
Et0/1     Link to SW2        connected    trunk        auto   auto unknown
Et0/2     Link to SW1        connected    trunk        auto   auto unknown
Et0/3     Link to SW1        connected    trunk        auto   auto unknown
Et1/0     Link to SRV1       connected    10           auto   auto unknown
Et1/1                        disabled     1            auto   auto unknown
Et1/2     Link to SW4        connected    trunk        auto   auto unknown
Et1/3     Link to SW4        connected    trunk        auto   auto unknown
Po1       EChannel to SW1    connected    trunk        auto   auto 
SW3#show spanning-tree vlan 10

VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     aabb.cc00.2000
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    24586  (priority 24576 sys-id-ext 10)
             Address     aabb.cc00.2000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr 
Et0/1               Desg FWD 100       128.2    Shr 
Et1/0               Desg FWD 100       128.5    Shr 
Et1/2               Desg FWD 100       128.7    Shr 
Et1/3               Desg FWD 100       128.8    Shr 
Po1                 Desg FWD 56        128.65   Shr 


SW3#
```

## Check configs

### Switch 4
```sh
SW4#show spanning-tree vlan 10

VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     aabb.cc00.2000
             Cost        100
             Port        7 (Ethernet1/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    28682  (priority 28672 sys-id-ext 10)
             Address     aabb.cc00.2100
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 100       128.1    Shr 
Et0/1               Altn BLK 100       128.2    Shr 
Et0/2               Desg FWD 100       128.3    Shr 
Et0/3               Desg FWD 100       128.4    Shr 
Et1/2               Root FWD 100       128.7    Shr 
Et1/3               Altn BLK 100       128.8    Shr 

          
SW4#
```
### Switch 1
```sh
SW1#show interface Port-channel 1
Port-channel1 is up, line protocol is up (connected) 
  Hardware is Ethernet, address is aabb.cc00.1430 (bia aabb.cc00.1430)
  Description: EChannel to SW3
  MTU 1500 bytes, BW 20000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Auto-duplex, Auto-speed, media type is unknown
  input flow-control is off, output flow-control is unsupported 
  Members in this channel: Et0/2 Et0/3 
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:00, output never, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/2000/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     322 packets input, 27220 bytes, 0 no buffer
     Received 288 broadcasts (0 multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 input packets with dribble condition detected
     321 packets output, 24228 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
SW1#
```

```sh
SW1#show etherchannel port-channel 
                Channel-group listing: 
                ----------------------

Group: 1 
----------
                Port-channels in the group: 
                ---------------------------

Port-channel: Po1    (Primary Aggregator)

------------

Age of the Port-channel   = 0d:00h:05m:38s
Logical slot/port   = 16/0          Number of ports = 2
HotStandBy port = null 
Port state          = Port-channel Ag-Inuse 
Protocol            =   LACP
Port security       = Disabled

Ports in the Port-channel: 

Index   Load   Port     EC state        No of bits
------+------+------+------------------+-----------
  0     00     Et0/2    Active             0
  0     00     Et0/3    Active             0
          
Time since last port bundled:    0d:00h:05m:11s    Et0/2
          
SW1#
```

```sh
SW1#show etherchannel summary
Flags:  D - down        P - bundled in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator

        M - not in use, minimum links not met
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
1      Po1(SU)         LACP      Et0/2(P)    Et0/3(P)    

SW1#
```

```sh
SW1#show etherchannel port-channel 
                Channel-group listing: 
                ----------------------

Group: 1 
----------
                Port-channels in the group: 
                ---------------------------

Port-channel: Po1    (Primary Aggregator)

------------

Age of the Port-channel   = 0d:00h:06m:16s
Logical slot/port   = 16/0          Number of ports = 2
HotStandBy port = null 
Port state          = Port-channel Ag-Inuse 
Protocol            =   LACP
Port security       = Disabled

Ports in the Port-channel: 

Index   Load   Port     EC state        No of bits
------+------+------+------------------+-----------
  0     00     Et0/2    Active             0
  0     00     Et0/3    Active             0
          
Time since last port bundled:    0d:00h:05m:49s    Et0/2
```

