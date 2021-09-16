# Configure Dynamic NAT and PAT


## Checking connection
### PC 1
```sh
PC1#ping 203.0.133.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

### Switch 2
```sh
SW2#ping 203.0.133.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
SW2#
```

### Router 1
```sh
R1#show ip nat statistics
Total active translations: 1 (1 static, 0 dynamic; 0 extended)
Peak translations: 1, occurred 00:00:37 ago
Outside interfaces:
  Ethernet0/3
Inside interfaces: 
  Ethernet0/1
Hits: 0  Misses: 0
CEF Translated packets: 0, CEF Punted packets: 0
Expired translations: 0
Dynamic mappings:

Total doors: 0
Appl doors: 0
Normal doors: 0
Queued Packets: 0
R1#
```

## Configure inside NAT
```sh
R1#
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface eth0/0
R1(config-if)#ip nat inside
R1(config-if)#exit
```

## Set rules
```sh
R1(config)#access-list 10 permit 10.10.0.0 0.0.255.255
R1(config)#ip nat pool NAT_POOL 198.51.100.100 198.51.100.149 netmask 255.255.255.0
R1(config)#ip nat inside source list 10 pool NAT_POOL
R1(config)#end
R1#
*Sep 15 21:28:15.136: %SYS-5-CONFIG_I: Configured from console by console
```

### Verifications
yes we can ping around.

## Clear translations
```sh
R1#clear ip nat translation *
```

## Reconfigure for PAT
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#no ip nat pool NAT_POOL
R1(config)#no ip nat inside source list 10 pool NAT_POOL
R1(config)#exit
R1#
*Sep 15 21:30:28.737: %SYS-5-CONFIG_I: Configured from console by console
R1#
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip nat inside source list 10 interface eth0/3 overload
R1(config)#end
R1#
*Sep 15 21:31:03.472: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

## Show Server 2 control plane connections
```sh

SRV2#show control-plane host open-ports
Active internet connections (servers and established)
Prot               Local Address             Foreign Address                  Service    State
```

## Show Router 1 NAT Statistics
```sh
R1#show ip nat statistics
Total active translations: 2 (1 static, 1 dynamic; 1 extended)
Peak translations: 6, occurred 00:03:21 ago
Outside interfaces:
  Ethernet0/3
Inside interfaces: 
  Ethernet0/0, Ethernet0/1
Hits: 25  Misses: 0
CEF Translated packets: 17, CEF Punted packets: 18
Expired translations: 3
Dynamic mappings:
-- Inside Source
[Id: 2] access-list 10 interface Ethernet0/3 refcount 1

Total doors: 0
Appl doors: 0
Normal doors: 0
Queued Packets: 0
R1#
```