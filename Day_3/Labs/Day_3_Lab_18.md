# Configure and Verify IPv4 ACLs

## Configure Router 1 

### Show current config
```sh
R1#show running-config | include access-list 10
access-list 10 permit 10.10.1.10
access-list 10 deny   10.10.1.0 0.0.0.255
access-list 10 permit 10.10.0.0 0.0.255.255
access-list 10 deny   10.0.0.0 0.255.255.255
access-list 10 permit any
R1#
```
### Configure group 10

```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface eth0/3
R1(config-if)#ip access-group 10 out
R1(config-if)#end
R1#
*Sep 15 19:23:13.845: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

### Check PC 1's ping
```sh
PC1#ping 203.0.133.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
```
#### Notes
This is wrong already, it should be able to ping here, yet it can't?  My access list here is the same as what the lab guide shows.

### Router 1 show
```sh
R1#show access-lists 10
Standard IP access list 10
    10 permit 10.10.1.10 (5 matches)
    20 deny   10.10.1.0, wildcard bits 0.0.0.255
    30 permit 10.10.0.0, wildcard bits 0.0.255.255
    40 deny   10.0.0.0, wildcard bits 0.255.255.255
    50 permit any
R1#
```

### Check if Router 2 can ping
```sh
R2#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
R2#
```

### Check ping from Router 1
```sh
R1#ping 203.0.133.30 source 198.51.100.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
Packet sent with a source address of 198.51.100.2 
U.U.U
Success rate is 0 percent (0/5)
```

#### Notes
Odd, my lab is different from the guide and I can't find anything I've missed.

The rules shown in `Router 1 show` are the same, but list `20` and `30` don't provide known matches like they do in the lab guide (lab guide shows `8` and `4` respectively).  Which is weird.

### Add deny to router 1
```sh
R1(config)#access-list 10 deny 10.10.2.0 0.0.0.255
R1(config)#end
R1#
*Sep 15 19:32:52.148: %SYS-5-CONFIG_I: Configured from console by console
R1#show access-list 10
Standard IP access list 10
    10 permit 10.10.1.10 (5 matches)
    20 deny   10.10.1.0, wildcard bits 0.0.0.255
    30 permit 10.10.0.0, wildcard bits 0.0.255.255 (4 matches)
    40 deny   10.0.0.0, wildcard bits 0.255.255.255
    50 permit any
    60 deny   10.10.2.0, wildcard bits 0.0.0.255
R1#
```

#### Show the ACL config failing on Router 1
Already used error
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#access-list 10 permit host 10.10.2.20
% Access rule can't be configured at higher sequence num as it is part of the existing rule at sequence num 30
R1(config)#
```

## Ping from SWitch 2

```sh

SW2#ping 203.0.133.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
SW2#
```
### Notes
Lab guide shows this as should work, which is weird that it doesn't still.  Seems to be the same issue.

## Disable access rule 60
```sh
1#
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip  access-list standard 10
R1(config-std-nacl)#no 60
R1(config-std-nacl)# end
```

### Verify config
```sh
R1#show access-lists 10
Standard IP access list 10
    10 permit 10.10.1.10 (5 matches)
    20 deny   10.10.1.0, wildcard bits 0.0.0.255
    30 permit 10.10.0.0, wildcard bits 0.0.255.255 (9 matches)
    40 deny   10.0.0.0, wildcard bits 0.255.255.255
    50 permit any
R1#
```

## Add more rules
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip access-list standard 10
R1(config-std-nacl)#24 permit host 10.10.2.20 
R1(config-std-nacl)#27 deny 10.10.2.0 0.0.0.255
R1(config-std-nacl)#end
```
### Verify
```sh
R1#show access-lists 10
Standard IP access list 10
    10 permit 10.10.1.10 (5 matches)
    24 permit 10.10.2.20
    20 deny   10.10.1.0, wildcard bits 0.0.0.255
    27 deny   10.10.2.0, wildcard bits 0.0.0.255
    30 permit 10.10.0.0, wildcard bits 0.0.255.255 (9 matches)
    40 deny   10.0.0.0, wildcard bits 0.255.255.255
    50 permit any
R1#
```

## Check ping from Server 1
```sh
SRV1#ping 203.0.133.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
SRV1#
```
### Notes
Different from lab guide, kinda almost as expected.  Should go through, gone through the lab 4 times up to here and still can't figure out where I messed up.
At least I can have some commands jotted down?

## Remove some of the ACLs on Router 1
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip access-list standard 10
R1(config-std-nacl)#no 10
R1(config-std-nacl)#no 20
R1(config-std-nacl)#end
*Sep 15 19:41:32.350: %SYS-5-CONFIG_I: Configured from console by console
```

### Verify
```sh
R1#show access-lists 10
Standard IP access list 10
    24 permit 10.10.2.20 (5 matches)
    27 deny   10.10.2.0, wildcard bits 0.0.0.255
    30 permit 10.10.0.0, wildcard bits 0.0.255.255 (9 matches)
    40 deny   10.0.0.0, wildcard bits 0.255.255.255
    50 permit any
R1#
```

## Ping Server 2 from Switch 1

```sh
SW1#ping SRV2
Translating "SRV2"...domain server (203.0.113.30) [OK]

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.113.30, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
SW1#
```

## Ping Server 2 from Switch 1

```sh
SW2#ping srv2
Translating "srv2"...domain server (203.0.113.30)
% Unrecognized host or address, or protocol not running.

SW2#ping 203.0.133.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
SW2#
```

## Configure more strict ACLs on Router 1

```sh
R1(config)#ip access-list extended PC1_TELNET
R1(config-ext-nacl)#deny udp any any
R1(config-ext-nacl)#permit tcp host 10.10.1.10 any eq 23
R1(config-ext-nacl)#deny tcp host 10.10.1.10 any
R1(config-ext-nacl)#permit ip any any
R1(config-ext-nacl)#end
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface eth0/0
R1(config-if)#ip access-group PC1_TELNET in
R1(config-if)#end
R1#
*Sep 15 19:46:01.823: %SYS-5-CONFIG_I: Configured from console by console
R1#
```
### Verify
#### On Router 1 
```sh
R1#show access-lists PC1_TELNET
Extended IP access list PC1_TELNET
    10 deny udp any any
    20 permit tcp host 10.10.1.10 any eq telnet
    30 deny tcp host 10.10.1.10 any
    40 permit ip any any
R1#
```

#### From PC1 to Router 2
```sh
PC1#ping R2
Translating "R2"...domain server (203.0.113.30)
% Unrecognized host or address, or protocol not running.

PC1#ping 198.51.100.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 198.51.100.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
PC1#
```
As expected since we blocked UDP, no DNS.

```sh
PC1#telnet 203.0.133.30 80 
Trying 203.0.133.30, 80 ... 
% Destination unreachable; gateway or host down
```
```sh
PC1#ping 10.10.2.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.2.20, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
PC1#
```

## Show Router 1's final access list.
```sh
R1#show access-lists
Standard IP access list 10
    24 permit 10.10.2.20 (9 matches)
    27 deny   10.10.2.0, wildcard bits 0.0.0.255 (10 matches)
    30 permit 10.10.0.0, wildcard bits 0.0.255.255 (26 matches)
    40 deny   10.0.0.0, wildcard bits 0.255.255.255
    50 permit any
Extended IP access list PC1_TELNET
    10 deny udp any any (1 match)
    20 permit tcp host 10.10.1.10 any eq telnet (1 match)
    30 deny tcp host 10.10.1.10 any (1 match)
    40 permit ip any any (10 matches)
R1#
```
### Notes
Interestingly enough my ACL rules all match according to the Lab guide, but a handful of the verification checks are just wrong.