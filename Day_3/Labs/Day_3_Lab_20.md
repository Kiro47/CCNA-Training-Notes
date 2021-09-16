# Configure Static NAT

## Configure the IP Address from the ISP
```ssh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface eth0/3
R1(config-if)#ip addr 198.51.100.2 255.255.255.0
R1(config-if)#exit
R1(config)#
R1(config)#ip route 0.0.0.0 0.0.0.0 198.51.100.1
````

### Verify
```sh
R1(config)#do show ip interface brief
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.10.1.1       YES NVRAM  up                    up      
Ethernet0/1                10.10.2.1       YES NVRAM  up                    up      
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                198.51.100.2    YES manual up                    up      
Serial1/0                  unassigned      YES NVRAM  administratively down down    
Serial1/1                  unassigned      YES NVRAM  administratively down down    
Serial1/2                  unassigned      YES NVRAM  administratively down down    
Serial1/3                  unassigned      YES NVRAM  administratively down down    
R1(config)#
R1(config)#do show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 198.51.100.1 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 198.51.100.1
      10.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
C        10.10.1.0/24 is directly connected, Ethernet0/0
L        10.10.1.1/32 is directly connected, Ethernet0/0
C        10.10.2.0/24 is directly connected, Ethernet0/1
L        10.10.2.1/32 is directly connected, Ethernet0/1
      198.51.100.0/24 is variably subnetted, 2 subnets, 2 masks
C        198.51.100.0/24 is directly connected, Ethernet0/3
L        198.51.100.2/32 is directly connected, Ethernet0/3
R1(config)#
```

## Check if Server 1 cannot ping

```sh
SRV1#ping 203.0.133.30
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.133.30, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
SRV1#
```
Correct

## Configure Router 1 to perform static NAT inside
```sh
R1(config)#interface eth0/1
R1(config-if)#ip nat inside

*Sep 15 21:14:16.329: %LINEPROTO-5-UPDOWN: Line protocol on Interface NVI0, changed state to up
R1(config-if)#
R1(config-if)#interface eth0/3
R1(config-if)#ip nat outside
R1(config-if)#exit
R1(config)#
```
This takes _several_ minutes, go get a drink.

```sh
R1(config)#ip nat inside source static 10.10.2.20 198.51.100.20
R1(config)#end
R1#
*Sep 15 21:18:19.182: %SYS-5-CONFIG_I: Configured from console by console
R1#
```