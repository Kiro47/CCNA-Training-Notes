# Inspect TCP/IP Applications

## Get open ports
```sh
R1#show control-plane host open-ports 
Active internet connections (servers and established)
Prot               Local Address             Foreign Address                  Service    State
 tcp                        *:22                         *:0               SSH-Server   LISTEN
 tcp                        *:23                         *:0                   Telnet   LISTEN
 tcp                        *:80                         *:0                HTTP CORE   LISTEN
 tcp                        *:80                         *:0                HTTP CORE   LISTEN
 tcp                       *:443                         *:0                HTTP CORE   LISTEN
 tcp                       *:443                         *:0                HTTP CORE   LISTEN
 udp                       *:123                         *:0                      NTP   LISTEN
```

## Telnet from PC1 to Router
```sh
PC1#telnet 10.10.1.1
Trying 10.10.1.1 ... Open


User Access Verification

Password: 
R1#
R1#show control-plane host open-ports 
Active internet connections (servers and established)
Prot               Local Address             Foreign Address                  Service    State
 tcp                        *:22                         *:0               SSH-Server   LISTEN
 tcp                        *:23                         *:0                   Telnet   LISTEN
 tcp                        *:80                         *:0                HTTP CORE   LISTEN
 tcp                        *:80                         *:0                HTTP CORE   LISTEN
 tcp                        *:23            10.10.1.10:13132                   Telnet ESTABLIS
 tcp                       *:443                         *:0                HTTP CORE   LISTEN
 tcp                       *:443                         *:0                HTTP CORE   LISTEN
 udp                       *:123                         *:0                      NTP   LISTEN

R1#
```

## And again from PC2
```sh
PC2#telnet 10.10.1.1
Trying 10.10.1.1 ... Open


User Access Verification

Password: 
R1#show control-plane host open-ports 
Active internet connections (servers and established)
Prot               Local Address             Foreign Address                  Service    State
 tcp                        *:22                         *:0               SSH-Server   LISTEN
 tcp                        *:23                         *:0                   Telnet   LISTEN
 tcp                        *:80                         *:0                HTTP CORE   LISTEN
 tcp                        *:80                         *:0                HTTP CORE   LISTEN
 tcp                        *:23            10.10.1.10:13132                   Telnet ESTABLIS
 tcp                       *:443                         *:0                HTTP CORE   LISTEN
 tcp                       *:443                         *:0                HTTP CORE   LISTEN
 tcp                        *:23            10.10.1.20:39998                   Telnet ESTABLIS
 udp                       *:123                         *:0                      NTP   LISTEN

R1#
```

## Disable HTTP Server
```sh
R1#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#no ip http server
R1(config)#exit
R1#
Sep 13 21:01:12.966: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

## Questions:

All things about service X on Y port.
