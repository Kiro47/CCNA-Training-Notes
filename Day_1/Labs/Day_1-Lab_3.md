# Configure switch:

## IP address range

```sh
SW1(config)# interface vlan 1  
SW1(config-if)# ip address 10.10.1.2 255.255.255.0  
SW1(config-if)# no shutdown
SW1(config-if)# exit
SW1(config-if)# 
SW1(config-if)# interface ethernet 0/0
SW1(config-if)# description Link to SW2
SW1(config-if)# interface ethernet 0/1
SW1(config-if)# description Link to PC1
SW1(config-if)# end
```

## Verify:

### Show vlan interface

```sh
SW1# show running-config interface vlan 1  
Building configuration...  
  
Current configuration : 59 bytes  
!  
interface Vlan1  
ip address 10.10.1.2 255.255.255.0  
end
```

### Verify interface
```sh
show ip interface brief
```

## Questions
Which command is used to set 172.20.137.1 as the default gateway on SwitchX?
> SwitchX(config)# ip default-gateway 172.20.137.1

Which mode is used to configure parameters that apply to the entire router?
> global configuration

