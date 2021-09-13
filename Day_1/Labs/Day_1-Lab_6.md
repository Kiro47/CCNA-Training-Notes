# Configure and Verify Layer 2 Discovery Protocols

## Showing CDP

### Switch 1
Show automatically gotten CDP Neighbors (it was enabled by default)
```sh
SW1#show cdp neighbors
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone, 
                  D - Remote, C - CVTA, M - Two-port Mac Relay 

Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
SW2              Eth 0/0           150              S I   Linux Uni Eth 0/0
```

```sh
SW1#show cdp neighbors detail
-------------------------
Device ID: SW2
Entry address(es): 
  IP address: 10.10.1.3
Platform: Linux Unix,  Capabilities: Switch IGMP 
Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet0/0
Holdtime : 164 sec

Version :
Cisco IOS Software, Solaris Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Experimental Version 15.1(20130919:231344) [dstivers-sept19-2013pm-team_track 107]
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Thu 19-Sep-13 22:38 by dstivers

advertisement version: 2
VTP Management Domain: ''
Duplex: half
Management address(es): 
  IP address: 10.10.1.3

SW1#
```

### Switch 2
```sh
SW2#show cdp neighbors
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone, 
                  D - Remote, C - CVTA, M - Two-port Mac Relay 

Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
SW1              Eth 0/0           178              S I   Linux Uni Eth 0/0
R1               Eth 0/1           159              R B   Linux Uni Eth 0/0
SW2#
```
```sh
SW2#show cdp neighbors detail
-------------------------
Device ID: SW1
Entry address(es): 
  IP address: 10.10.1.2
Platform: Linux Unix,  Capabilities: Switch IGMP 
Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet0/0
Holdtime : 146 sec

Version :
Cisco IOS Software, Solaris Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Experimental Version 15.1(20130919:231344) [dstivers-sept19-2013pm-team_track 107]
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Thu 19-Sep-13 22:38 by dstivers

advertisement version: 2
VTP Management Domain: ''
Duplex: half
Management address(es): 
  IP address: 10.10.1.2

-------------------------
Device ID: R1
Entry address(es): 
  IP address: 10.10.1.1
Platform: Linux Unix,  Capabilities: Router Source-Route-Bridge 
Interface: Ethernet0/1,  Port ID (outgoing port): Ethernet0/0
Holdtime : 127 sec

          
Version : 
Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.5(2)T, DEVELOPMENT TEST SOFTWARE
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2015 by Cisco Systems, Inc.
Compiled Thu 26-Mar-15 07:36 by prod_rel_team
          
advertisement version: 2
Duplex: half
Management address(es): 
  IP address: 10.10.1.1
```

### Router 1
```sh

R1#show cdp neighbors
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone, 
                  D - Remote, C - CVTA, M - Two-port Mac Relay 

Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
SW2              Eth 0/0           131              S I   Linux Uni Eth 0/1
R2               Eth 0/1           159              R B   Linux Uni Eth 0/0

Total cdp entries displayed : 2
R1#
```sh
R1#show cdp neighbors detail
-------------------------
Device ID: SW2
Entry address(es): 
  IP address: 10.10.1.3
Platform: Linux Unix,  Capabilities: Switch IGMP 
Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet0/1
Holdtime : 121 sec

Version :
Cisco IOS Software, Solaris Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Experimental Version 15.1(20130919:231344) [dstivers-sept19-2013pm-team_track 107]
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Thu 19-Sep-13 22:38 by dstivers

advertisement version: 2
VTP Management Domain: ''
Native VLAN: 1
Duplex: half
Management address(es): 
  IP address: 10.10.1.3

-------------------------
Device ID: R2
Entry address(es): 
  IP address: 192.168.3.2
Platform: Linux Unix,  Capabilities: Router Source-Route-Bridge 
Interface: Ethernet0/1,  Port ID (outgoing port): Ethernet0/0
Holdtime : 148 sec

Version : 
Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.5(2)T, DEVELOPMENT TEST SOFTWARE
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2015 by Cisco Systems, Inc.
Compiled Thu 26-Mar-15 07:36 by prod_rel_team
          
advertisement version: 2
Duplex: half
Management address(es): 
  IP address: 192.168.3.2
          
          
Total cdp entries displayed : 2
```

### Router 2
```sh
R2#show cdp neighbors
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone, 
                  D - Remote, C - CVTA, M - Two-port Mac Relay 

Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
R1               Eth 0/0           159              R B   Linux Uni Eth 0/1

Total cdp entries displayed : 1
R2#
```
```sh
R2#show cdp neighbors detail
-------------------------
Device ID: R1
Entry address(es): 
  IP address: 192.168.3.1
Platform: Linux Unix,  Capabilities: Router Source-Route-Bridge 
Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet0/1
Holdtime : 133 sec

Version :
Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.5(2)T, DEVELOPMENT TEST SOFTWARE
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2015 by Cisco Systems, Inc.
Compiled Thu 26-Mar-15 07:36 by prod_rel_team

advertisement version: 2
Duplex: half
Management address(es): 
  IP address: 192.168.3.1


Total cdp entries displayed : 1
```

## Using LLDP

### Disable CDP first

#### Switch 1

```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#no cdp run
```
#### Switch 2
```sh
SW2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#no cdp run
```

#### Router 1
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#no cdp run
```

#### Router 2
```sh
R2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#no cdp run
```

### Run LLDP

#### Switch 1

```sh
SW1(config)#lldp run
SW1(config)#exit
SW1#
*Sep 13 22:30:16.106: %SYS-5-CONFIG_I: Configured from console by console
```
#### Switch 2
```sh
SW2(config)#lldp run
SW2(config)#exit
SW2#
*Sep 13 22:30:30.181: %SYS-5-CONFIG_I: Configured from console by console
```

#### Router 1
```sh
R1(config)#lldp run
R1(config)#exit
R1#
*Sep 13 22:30:43.913: %SYS-5-CONFIG_I: Configured from console by console
```

#### Router 2
```sh
R2(config)#lldp run
R2(config)#exit
R2#
*Sep 13 22:30:56.171: %SYS-5-CONFIG_I: Configured from console by console
```

### Show LLDP info
#### Switch 1
```sh
SW1#show lldp neighbors 
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other

Device ID           Local Intf     Hold-time  Capability      Port ID
SW2                 Et0/0          120                        Et0/0

Total entries displayed: 1
```
```sh
SW1#
SW1#show lldp neighbors detail
------------------------------------------------
Chassis id: aabb.cc00.2400
Port id: Et0/0
Port Description: Ethernet0/0
System Name: SW2

System Description: 
Cisco IOS Software, Solaris Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Experimental Version 15.1(20130919:231344) [dstivers-sept19-2013pm-team_track 107]
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Thu 19-Sep-13 22:38 by dstivers

Time remaining: 103 seconds
System Capabilities: B,R
Enabled Capabilities - not advertised
Management Addresses:
    IP: 10.10.1.3
Auto Negotiation - not supported
Physical media capabilities - not advertised
Media Attachment Unit type - not advertised
Vlan ID: - not advertised

Total entries displayed: 1
```

> In the virtual lab environment, when you run the LLDP on a switch, it can
discover neighbor routers and switches. But LLDP enabled router will discover
only neighbor routers.


* Pros of CDP:
    * CDP gets the Platform Column Info.
* Pros of LLDP:
    * Can show Linux Servers
* Cons
    * Security Nightmare
