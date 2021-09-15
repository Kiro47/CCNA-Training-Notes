# Configure Default Gateway

## Configure the default gateway
Yes it's that easy

### PC 1

See gateway isn't set

```sh
PC1#show ip route
Default gateway is not set

Host               Gateway           Last Use    Total Uses  Interface
ICMP redirect cache is empty
PC1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.1.10              -   aabb.cc00.9900  ARPA   Ethernet0/0
PC1#
```

Learn associated MAC addresses by ping

```sh
PC1#ping 10.10.1.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.20, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#ping 10.10.1.1 
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/201/1001 ms
PC1#ping 10.10.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.1.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#
```

See new data:
```sh
PC1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.1.1               0   aabb.cc00.9800  ARPA   Ethernet0/0
Internet  10.10.1.2               0   aabb.cc80.a600  ARPA   Ethernet0/0
Internet  10.10.1.10              -   aabb.cc00.9900  ARPA   Ethernet0/0
Internet  10.10.1.20              0   aabb.cc00.9a00  ARPA   Ethernet0/0
PC1#
```

Try to ping host on a different subnet
```sh
PC1#ping 192.168.3.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.3.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
PC1#show arp        
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.1.1               0   aabb.cc00.9800  ARPA   Ethernet0/0
Internet  10.10.1.2               0   aabb.cc80.a600  ARPA   Ethernet0/0
Internet  10.10.1.10              -   aabb.cc00.9900  ARPA   Ethernet0/0
Internet  10.10.1.20              1   aabb.cc00.9a00  ARPA   Ethernet0/0
Internet  192.168.3.2             0   aabb.cc00.9800  ARPA   Ethernet0/0
PC1#
```

There is an ARP entry for the address on a different subnet, which is the same MAC as our actual gateway `10.10.1.1` (which we broadcasted to).  This is a feature of Cisco IOS routers called "Proxy ARP".  So when a host doesn't have a default gateway that is configured it ARPs for all addresses, and sent an ARP with it's own MAC address it'd use to forward traffic.

Is Proxy Arp common in other Routers?  

Proxy Arp used to be the default thing when default gateways didn't exist.  So we used to have ARP tables with thousands of entries with the same MAC address (as where all the entries exited from).  So everyone supported them at one point in time, but some may not anymore because it's a pretty bad idea.


### Actually configure the default gateway

```sh
PC1#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
PC1(config)#ip default gateway 10.10.1.1
                       ^
% Invalid input detected at '^' marker.

PC1(config)#ip default-gateway 10.10.1.1
PC1(config)#end
PC1#
*Sep 14 14:39:40.093: %SYS-5-CONFIG_I: Configured from console by console
```
```sh
PC1#show ip route
Default gateway is 10.10.1.1

Host               Gateway           Last Use    Total Uses  Interface
ICMP redirect cache is empty
```
Clear the old ARP entry
```sh
PC1#clear ip arp 192.168.3.2
PC1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.1.1               2   aabb.cc00.9800  ARPA   Ethernet0/0
Internet  10.10.1.2               2   aabb.cc80.a600  ARPA   Ethernet0/0
Internet  10.10.1.10              -   aabb.cc00.9900  ARPA   Ethernet0/0
Internet  10.10.1.20              2   aabb.cc00.9a00  ARPA   Ethernet0/0
```

See that it used the gateway instead of broadcasting
```sh
PC1#ping 192.168.3.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.3.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
PC1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.1.1               3   aabb.cc00.9800  ARPA   Ethernet0/0
Internet  10.10.1.2               2   aabb.cc80.a600  ARPA   Ethernet0/0
Internet  10.10.1.10              -   aabb.cc00.9900  ARPA   Ethernet0/0
Internet  10.10.1.20              3   aabb.cc00.9a00  ARPA   Ethernet0/0
```
