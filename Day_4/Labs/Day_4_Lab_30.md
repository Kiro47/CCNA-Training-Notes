# Lab 30

Using NTP

## Check clocks

### Switch 1
```sh
SW1#show clock
*13:49:24.916 PST Thu Sep 16 2021
SW1#
*Sep 16 21:49:44.178: %LINK-3-UPDOWN: Interface Vlan1, changed state to up
*Sep 16 21:49:45.178: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
SW1#s
```

### Switch 2
```sh
SW2#show clock
*13:51:16.037 PST Thu Sep 16 2021
SW2#
```

### Router 1
```sh
R1#show clock
*13:51:36.413 PST Thu Sep 16 2021
R1#
```

## Configure Router 1 as an NTP server
```sh
R1#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface Lo 
R1(config)#interface Loo
R1(config)#interface Loopback 10
R1(config-if)#ip addr 10.
*Sep 16 21:52:11.048: %LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback10, changed state to up
R1(config-if)#ip addr 10.10.4.1 255.255.255.0
R1(config-if)#exit
R1(config)#ntp master 3
R1(config)#ntp source Loopback 10
R1(config)#end
R1#
Sep 16 21:52:29.498: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

## Set switches to use Router 1 as NTP server
```sh
SW1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#ntp server 10.10.4.1
SW1(config)#end
```

### Verify
```sh
SW1#
*Sep 16 21:53:10.284: %SYS-5-CONFIG_I: Configured from console by console
SW1#show ntp associatio
SW1#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~10.10.4.1       127.127.1.1      3      1     64     1  1.000  -0.500 939.36
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
SW1#show ntp status
Clock is synchronized, stratum 4, reference is 10.10.4.1      
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 1200 (1/100 of seconds), resolution is 4000
reference time is E4EE3B48.DEF9DD88 (13:53:12.871 PST Thu Sep 16 2021)
clock offset is -0.5000 msec, root delay is 2.00 msec
root dispersion is 5379.67 msec, peer dispersion is 189.48 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 64, last update was 10 sec ago.
SW1#
```
```sh
SW1#show clock
13:55:34.076 PST Thu Sep 16 2021
SW1#
```

## Set switches to use Router 2 as NTP server
```sh
SW2#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#ntp server 10.10.4.1
SW2(config)#end
```

### Verify
```sh
SW2#
*Sep 16 21:54:01.467: %SYS-5-CONFIG_I: Configured from console by console
SW2#show ntp associations

  address         ref clock       st   when   poll reach  delay  offset   disp
*~10.10.4.1       127.127.1.1      3      0     64     1  1.000  -0.500 1939.2
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
SW2#show ntp status
Clock is synchronized, stratum 4, reference is 10.10.4.1      
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 700 (1/100 of seconds), resolution is 4000
reference time is E4EE3B7B.EED91918 (13:54:03.933 PST Thu Sep 16 2021)
clock offset is -0.5000 msec, root delay is 1.00 msec
root dispersion is 4504.26 msec, peer dispersion is 939.35 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 64, last update was 5 sec ago.
SW2#
```
```sh
SW2#show clock
13:55:08.227 PST Thu Sep 16 2021
SW2#
```