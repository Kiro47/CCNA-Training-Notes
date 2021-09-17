# Implementing Device Hardening

## Securing unused ports

If you're not using it, turn it off.
### Shutdown port
```sh
SwitchX(config)# interface range GigabitEthernet0/1 – 2  
SwitchX(config-if-range)# shutdown  
SwitchX(config-if-range)# end  
SwitchX # show running-config  
<... output omitted ...>!  
interface GigabitEthernet0/1  
shutdown  
!  
interface GigabitEthernet0/2  
shutdown  
<... output omitted ...>
```
### Disable port and access it to a dead vlan
```sh
SwitchX(config)# interface range GigabitEthernet0/1 – 2  
SwitchX(config-if-range)# switchport mode access  
SwitchX(config-if-range)# switchport access vlan 888  
SwitchX(config-if-range)# shutdown  
SwitchX(config-if-range)# exit  
  
SwitchX(config)# vlan 888  
SwitchX(config-vlan)# name Parking VLAN  
SwitchX(config-vlan)# shutdown
```

## Infrastructure ACL
ACL rules for your infrastructure, specifying what can use BGP/SNMP/ etc.

## Disable unused services

### See what's open

```sh
Router# show control-plane host open-ports
```

#### Disable
```sh
Router# no ip http server
```

#### CDP
```sh
Router# no cdp enable
Router# no cdp run
```
`run` is across device

`enable` is per interface

## Port Security

* Static MAC Addresses
    * Single MAC allowed
* Dynamic MAC Addresses
    * One out of list of MACs allowed
    * Or allows X number of MAC addresses within Y time period.
* Sticky MAC Addresses
    * Allow the first MAC address register, then don't allow anything else.

### Violation policies

* Protect
    * Offending frame is dropped
* Restrict
    * Offending frame is dropped and logged
* Shutdown
    * Shuts the port down
* Shutdown vlan
    * If activated, shuts down the entire vlan

### Configuring
1. Port can't be in DTP dynamic auto
2. Set maximum number of mac addresses (optional)
3. Specify allowed MAC addresses (optional)
4. Set violation modes (optional)
5. Set aging parameters. (optional)
6. Enable port security

### Show
```sh
Switch# show port-security
```
Can specify interface

## Mitigating VLAN attacks
### Other notes

* Shutting down a port does not disable DTP

## DHCP Snooping
### DHCP Spoofing
Attacker sends a DHCP offer message before the actual DHCP server can respond, so the attacker gets the default dns/gateway/etc.

### DHCP Starvation
Attacker floods dhcp requests with spoofed MAC addresses, and makes the pool run out.


## DHCP Snooping

```sh
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10-19
Switch(config)# interface GigabitEthernet0/24
Switch(config)# ip dhcp snooping trust
```

* Configures which ports DHCP requests should be able to go to
* Records the dhcp handshake.

## Dynamic ARP inspects

```sh
SW(config)# ip dhcp snooping  
SW(config)# ip dhcp snooping vlan 10  
SW(config)# ip arp inspection vlan 10  
SW(config)# interface ethernet 0/0  
SW(config-if)# ip dhcp snooping trust  
SW(config-if)# ip arp inspection trust
```

