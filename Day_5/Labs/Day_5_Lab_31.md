# Lab 31: Create the Cisco IOS Image Backup

## show
```sh
R1# show flash:  
-#- --length-- -----date/time------ path  
1 50933220 Oct 24 2013 14:03:04 +00:00 c2800nm-advipservicesk9-mz.124-15.T.bin  
2 1823 Oct 24 2013 14:10:58 +00:00 sdmconfig-2811.cfg  
3 1826 Oct 24 2013 14:11:02 +00:00 sdmconfig-28xx.cfg  
4 527849 Oct 24 2013 14:11:08 +00:00 128MB.sdf  
5 1038 Oct 24 2013 14:11:14 +00:00 home.shtml  
6 6036480 Oct 24 2013 14:11:42 +00:00 sdm.tar  
7 861696 Oct 24 2013 14:11:54 +00:00 es.tar  
8 113152 Oct 24 2013 14:12:00 +00:00 home.tar  
9 1164288 Oct 24 2013 14:12:08 +00:00 common.tar  
10 793739 Oct 24 2013 14:12:22 +00:00 256MB.sdf  
  
3522560 bytes available (60461056 bytes used)
```

## Copy
```sh
R1# copy flash: tftp:  
Source filename []? c2800nm-advipservicesk9-mz.124-15.T.bin  
Address or name of remote host []? 172.16.1.100  
Destination filename [c2800nm-advipservicesk9-mz.124-15.T.bin]? <Enter>  
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!  
50933220 bytes copied in 265.920 secs (191536 bytes/sec)
```
