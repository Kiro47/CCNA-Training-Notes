# Lab 32: Upgrade the Cisco IOS Image

## Reload
```sh
R1# reload
Proceed with reload? [confirm]
```

## Copy image from remote tftp
```sh
R1# copy tftp: flash:  
Address or name of remote host []? 172.16.1.100  
Source filename []? c2800nm-advipservicesk9-mz.124-20.T.bin  
Destination filename []? c2800nm-advipservicesk9-mz.124-20.T.bin  
Accessing tftp://172.16.1.100/c2800nm-advipservicesk9-mz.124-20.T.bin...  
Loading c2800nm-advipservicesk9-mz.124-20.T.bin from 172.16.1.100 (via
FastEthernet0/0):
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!  
[OK - 50933220 bytes]  
  
50933220 bytes copied in 287.540 secs (177134 bytes/sec)
```

## Boot new image
```sh
R1# configure terminal
R1(config)# boot system flash c2800nm-advipservicesk9-mz.124-20.T.bin 
R1(config)# exit 
```

#### Verify
```sh
R1# show version 
```

### Copy to startup
```sh
R1# copy running-config startup-config
```

### Confirm
```sh
R1# show version
```