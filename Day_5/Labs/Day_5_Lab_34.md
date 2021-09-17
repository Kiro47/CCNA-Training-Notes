# Lab 34: Secure console and remote access

## Set password 
```sh
R1>enable  
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#enable password Password123 
R1(config)#end
R1#
*Sep 17 17:45:03.968: %SYS-5-CONFIG_I: Configured from console by console
```

### Verify
```sh
*Sep 17 17:45:03.968: %SYS-5-CONFIG_I: Configured from console by console
R1#disable 
R1>enable
Password: 
R1#
R1#show running-config | include enab
enable password Password123
```

## Set hashed password
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#enable secret Secret123
R1(config)#end
R1#
*Sep 17 17:46:26.072: %SYS-5-CONFIG_I: Configured from console by console
R1#
```
### Verify
```sh
R1#
R1#disable
R1>enable
Password: 
R1#show running-config | include enable
enable secret 4 9h/bNbZRK8Hm9J2ONmwUdf0KoztPJewuR2NseOBKzM6
enable password Password123
R1#
```
> Note that secret takes priority over password.  Once secret is set, password no longer is used.


## Change how password is shown
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#service password-encryption
R1(config)#end
*Sep 17 17:48:28.664: %SYS-5-CONFIG_I: Configured from console by console
R1#show running-config | include enable
enable secret 4 9h/bNbZRK8Hm9J2ONmwUdf0KoztPJewuR2NseOBKzM6
enable password 7 00341215174C04140B701E1D
R1#
```

## Secure specific access
### Console
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#line console 0
R1(config-line)#login
% Login disabled on line 0, until 'password' is set
R1(config-line)#password Console123
R1(config-line)#end
```
#### Verify
```sh
R1#
*Sep 17 17:49:43.865: %SYS-5-CONFIG_I: Configured from console by console
R1#show running-config | section line
line con 0
 password 7 0327540515002D491F5B4A
 logging synchronous
 login
line aux 0
line vty 0 4
 login
 transport input all
R1# logout
R1 con0 is now available





Press RETURN to get started.



User Access Verification

Password: 
R1>enable
Password: 
R1#
```

### Remote
```sh
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#line vty 0 4
R1(config-line)#login
% Login disabled on line 2, until 'password' is set
% Login disabled on line 3, until 'password' is set
% Login disabled on line 4, until 'password' is set
% Login disabled on line 5, until 'password' is set
% Login disabled on line 6, until 'password' is set
R1(config-line)#password VTYPass
R1(config-line)#end
R1#
*Sep 17 17:51:11.752: %SYS-5-CONFIG_I: Configured from console by console
R1#
```
#### Verify from PC 1
```sh

PC1>telnet 10.10.1.1
Trying 10.10.1.1 ... Open


User Access Verification

Password: 
R1>
[Connection to 10.10.1.1 closed by foreign host]
```

### Options
```sh
R1#configure terminal
R1(config)#username ?
  WORD  User name

R1(config)#username admin ?
  aaa                  AAA directive
  access-class         Restrict access by access-class
  autocommand          Automatically issue a command after the user logs in
  callback-dialstring  Callback dialstring
  callback-line        Associate a specific line with this callback
  callback-rotary      Associate a rotary group with this callback
  dnis                 Do not require password when obtained via DNIS
  nocallback-verify    Do not require authentication after callback
  noescape             Prevent the user from using an escape character
  nohangup             Do not disconnect after an automatic command
  nopassword           No password is required for the user to log in
  one-time             Specify that the username/password is valid for only one
                       time
  password             Specify the password for the user
  privilege            Set user privilege level
  secret               Specify the secret for the user
  user-maxlinks        Limit the user's number of inbound links
  view                 Set view name
  <cr>

R1(config)#username admin 
R1(config)#username admin secret ?
  0     Specifies an UNENCRYPTED secret will follow
  4     Specifies a SHA256 ENCRYPTED secret will follow
  5     Specifies a MD5 ENCRYPTED secret will follow
  LINE  The UNENCRYPTED (cleartext) user secret
```

#### Set password for specific user
```sh
R1(config)#username admin secret Cisco123
```
##### Verify
```sh
R1(config)#do show running-config | include user
username admin secret 4 vwcGVdcUZcRMCyxaH2U9Y/PTujsnQWPSbt.LFG8lhTw
R1(config)#
```

### Disable general login and require user login
```sh
R1(config)#line vty
% Incomplete command.

R1(config)#line vty 0 4
R1(config-line)#login local
R1(config-line)#no password
R1(config-line)#end
R1#
*Sep 17 17:55:08.362: %SYS-5-CONFIG_I: Configured from console by console
R1#
```

#### Verify from PC1
```sh
PC1>telnet 10.10.1.1
Trying 10.10.1.1 ... Open

User Access Verification

Username: admin
Password: 
R1>
```

## Enable SSH
```sh
R1#
R1#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip domain-name ccna.lab
R1(config)#crypto key generate rsa
The name for the keys will be: R1.ccna.lab
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
% Generating 2048 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 2 seconds)

R1(config)#
*Sep 17 17:57:21.034: %SSH-5-ENABLED: SSH 1.99 has been enabled
R1(config)#
R1(config)#ip ssh version 2
R1(config)#end
```