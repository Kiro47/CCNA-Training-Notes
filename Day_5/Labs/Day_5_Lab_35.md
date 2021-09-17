# Lab 35: Enable and limit remote access connectivity

```sh
R1#show access-lists 
```

## Configure IP limits
### Create access list
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#access-list 1 permit 10.10.1.10
R1(config)#access-list 1 permit 10.10.1.20
R1(config)#access-list 1 deny any log
R1(config)#
```

### Apply access list
```sh
R1(config)#
R1(config)#line vty 0 4
R1(config-line)#access-class 1 in
R1(config-line)#end
R1#
*Sep 17 18:03:23.468: %SYS-5-CONFIG_I: Configured from console by console
R1#
```
#### Verify from PC 1
```sh
PC1>ssh -l admin 10.10.1.1
Password: 

R1>logout

[Connection to 10.10.1.1 closed by foreign host]
PC1>
```

#### Verify from PC2
```sh

PC2>ssh -l admin 10.10.1.1
Password: 
R1>logout

[Connection to 10.10.1.1 closed by foreign host]
PC2>
```
#### Verify from Switch 1
```sh
SW1>ssh -l admin 10.10.1.1
% Connection refused by remote host

SW1>
```

#### Show rules
```sh
R1#show access-lists 
Standard IP access list 1
    10 permit 10.10.1.10 (2 matches)
    20 permit 10.10.1.20 (2 matches)
    30 deny   any log (1 match)
R1#
```

## Configure banners

### Login banner
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#banner login x
Enter TEXT message.  End with the character 'x'.
Access for authorized users only, etc etc
x
```

### Exec banner
```sh
R1(config)#banner exec x
Enter TEXT message.  End with the character 'x'.
AUTHORIZED ACCESS ONLY!
x
```

### Verify both from PC1
```sh
PC1>ssh -l admin 10.10.1.1

Access for authorized users only, etc etc

Password: 

AUTHORIZED ACCESS ONLY!
R1>
```