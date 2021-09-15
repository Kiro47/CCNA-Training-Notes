# Implemetning VLAN's and Trunks (cont)

## VLAN Design considerations
* Maximum number of VLANs is switch-dependant
* VLAN 1 is the factory default Ethernet VLAN
* Keep management traffic in a separate VLAN
* Change the native VLAN to something other than VLAN 1.

### When Trunking
* Make sure VLAN is 802.1Q is used on both ends
* Only allow specific VLANs to traverse through the trunk port
* DTP manages trunk negations on Cisco switches.
    * Dynamic Trunking Protocol
    * Don't use it it's proprietary
        * Even Cisco says don't use it.

# Routing between VLAN's

## Purpose of Inter-VLAN Routing

* VLANs have these characteristics
    * A VLAN creates a separate Layer 2 broadcast domain
    * Traffic cannot be switched between VLANs
    * Each VLAN is mapped to a separate subnet
    * Routing is necessary to forward traffic between VLAN's

## Options

### Router with a seperate interface for each VLAN
Each VLAN has it's own gateway, goes up the access port to the router, then send it down the other VLAN's gateway into the subnet.

Essentially you just treat them like completely separate networks each with their own interface on the Router.

### Router on a stick
Creates virtual interfaces on the router for each.
IE:
```yaml
VLAN10:
    Subnet: 10.1.10.100/24
    Router Interface: Fa0/0.10-10.1.10.1
VLAN20:
    Subnet: 10.1.20.100/24
    Router Interface: Fa0/0.20-10.1.20.1
```
Both Router interfaces are the same connection link.

#### Configuration example

```sh
Router(config)# interface GigabitEthernet 0/0.10  
Router(config-subif)# encapsulation dot1q 10  
Router(config-subif)# ip address 10.1.10.1 255.255.255.0  
Router(config-subif)# interface GigabitEthernet 0/0.20  
Router(config-subif)# encapsulation dot1q 20  
Router(config-subif)# ip address 10.1.20.1 255.255.255.0
```
Syntax:
`encapsulation dot1q <vlan id>`
Encapsulation _has_ to be done before assigning the IP address.

Router sees these as 

With associated Switch config:
```sh
Switch(config)# interface FastEthernet 0/13  
Switch(config-if)# switchport mode trunk  
Switch(config-if)# interface FastEthernet 0/1  
Switch(config-if)# switchport mode access  
Switch(config-if)# switchport access vlan 10  
Switch(config-if)# interface FastEthernet 0/3  
Switch(config-if)# switchport mode access  
Switch(config-if)# switchport access vlan 20
```

### Layer 3 switch
Basically just set it
```sh
ip routing  
!  
interface Vlan10  
ip address 10.1.10.1 255.255.255.0  
no shutdown  
!  
interface Vlan20  
ip address 10.1.20.1 255.255.255.0  
no shutdown
```

# Improving Redundant Switch Topologies with EtherChannel

## Ether Channel Overview
* When traffic from multiple devices is aggregated into one link, congressions may occur.
* Solutions:
    * Upgrade links
        * Expensive
        * Not scalable indefinitely
    * Aggregate multiple links into one

Aggregated logical channels are called a "port-channel"

### EtherChannel Characteristics
* Logical aggregations between links (LAG)
* High bandwidth
* Load sharing across
* One logical port to STP
* Redundancy

## Configuring and Verifying EtherChannel

LACP Modes:
* passive
    * passively waits for other sides to negotiate
* active
    * actively negotiating EtherChannel link establishment

Static:
* Manual, of course
    * Unconditional EtherChannel member, no negotiations performed.

```sh
Switch(config)# interface range GigabitEthernet0/1 - 4
Switch(config-if)# shutdown
Switch(config-if)# channel-group 1 mode active
Switch(config-if)# exit
Switch(config)# interface port-channel 1
Switch(config)# switchport mode trunk
Switch(config)# switchport trunk allowed vlan 1,2,20
Switch(config)# interface range GigabitEthernet0/1 - 4
Switch(config-if)# no shutdown
```

### Verify
```sh
SW2# show etherchannel summary
SW2# show etherchannel Port-channel
```

