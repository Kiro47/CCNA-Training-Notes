# Securing administrative access

## Threats
* Remote access
* Local Access / Physical
* Environmental threats
* Electrical threats
* Maintenance threats
    * Improper handling
    * Poor cabling
    * Improper labeling


## General Security

Password:
* Guessing
* Brute force
* dictionary

Password requirements:
* password length
* large character set

Password management:
* Storage
* Protection
* Password changes

Password Alternatives:
* Multi-factor auth
* Digital certificates
* Biometrics

## Securing Console Access

### Show password
```sh
Switch(config)# enable secret sanfran  
Switch(config)# enable password C1sco123
Switch# show running-config | include enable  
enable secret 5 $1$WPHF$uWo4ucV0/vA1/abu6LlWQ1  
enable password C1sco123
```

`Enable password: ` Uses plaintext password

`Enable Secret: ` Uses hashed password

```sh

```

### Securing access to the console
```sh
Switch(config)# line console 0  
Switch(config-line)# password C1sco123  
Switch(config-line)# login  
Switch(config-line)# end  
Switch# show running-config partition line  
line con 0  
exec-timeout 0 0  
privilege level 15  
password C1sco123  
logging synchronous  
login
```
The `login` parameter says you have to login. `login` says use the line (plaintext) password.

```sh
Switch# configure terminal  
Switch(config)# service password-encryption  
Switch(config)# end  
Switch# show running-config partition line  
line con 0  
exec-timeout 0 0  
privilege level 15  
password 7 06255E324F41584B56  
logging synchronous  
login
```
`password-encryption` uses a vigenere cipher

### Configure username and password
```sh
Switch# configure terminal  
Switch(config)# username cisco password C1sco123  
Switch(config)# line console 0  
Switch(config-line)# login local  
Switch(config-line)# end  
Switch# show running-config | include username  
username cisco password C1sco123  
Switch# show running-config partition line  
line con 0  
exec-timeout 0 0  
privilege level 15  
logging synchronous  
login local
```
```sh
Switch# configure terminal  
Switch(config)# username cisco secret C1sco123  
Switch(config)# line console 0  
Switch(config-line)# login local  
Switch(config-line)# end  
Switch# show running-config | include username  
username cisco secret 4 Q0UVR5.QRaZf.PwUsvR7kxkdDLpoWHHZCd88ZDRyLhY  
Switch# show running-config partition line  
line con 0  
exec-timeout 0 0  
privilege level 15  
logging synchronous  
login local
```

#### Configure timeout

Basically a screensaver before auto-disconnect
```sh
Switch# configure terminal  
Switch(config)# line console 0  
Switch(config-line)# exec-timeout 5
```

## Securing remote access

```sh
Switch(config)# line vty 0 15  
Switch(config-line)# login  
Switch(config-line)# password CiScO
Switch(config-line)# exec-timeout 5
```
Allow up to 16 remote users with login and password and 5 minute timeout

### SSH Config
```sh
Switch(config)# hostname SwitchX  
SwitchX(config)# ip domain-name cisco.com  
SwitchX(config)# username user1 secret C1sco123  
SwitchX(config)# crypto key generate rsa modulus 2048  
The name for the keys will be: SwitchX.cisco.com  
% The key modulus size is 2048 bits  
% Generating 2048 bit RSA keys, keys will be nonexportable...  
[OK] (elapsed time was 1 seconds)  
SwitchX(config)#  
*Dec 25 13:37:42.000 %SSH-5-ENABLED: SSH 1.99 has been enabled  
SwitchX(config)# line vty 0 15  
SwitchX(config-line)# login local  
SwitchX(config-line)# transport input ssh  
SwitchX(config-line)# exit  
SwitchX(config)# ip ssh version 2
```
Need to specify version 2 for some reason

#### Show who else is logged in via ssh
```sh
Switch# show ssh
```

#### Show all logged in
```sh
Switch# show user
Switch# who
```

## Configuring login banner

```sh
Switch(config)# banner login "Access for authorized users only. Please enter
your username and password."
```
Any character can be a delimiter (here it is `"`)

### Configure MOTD banner
```sh
Switch(config)# banner motd "X is out"
```
Shown after login

## Configuring remote ACLs

```sh
Router(config)# access-list 1 permit 10.1.1.0 0.0.0.255  
Router(config)# access-list 1 deny any log  
Router(config)# line vty 0 15  
Router(config-line)# access-class 1 in
```

Deny any is implicit, but we restate it here so we can set it to `log`.

## External authentication options

`RADIUS: ` Remote Authentication Dial-In user Service

`TACASCS+: ` Terminal Access Controller Access Control Sytstem Plus (Cisco specific)

### IEEE 802.1X
Cannot even use the network until you're authenticated.

Client: 

Authenticator:

Authentication server (RADIUS):