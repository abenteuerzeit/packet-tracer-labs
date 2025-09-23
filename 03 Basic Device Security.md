# Command Reference

```
Switch>enable
Switch#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
```

## Change Name

```
Switch(config)#hostname SW1
```

## Configure an unencrypted priveleged EXEC mode password

```
SW1(config)#enable password CCNA
```

## View the password

```
SW1(config)#do show running-config
Building configuration...

Current configuration : 1100 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW1
!
enable password CCNA
!
!
```

## Encrypt passwords

```
SW1(config)#service password-encryption 
```

```
SW1(config)#do sh run
Building configuration...

Current configuration : 1105 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname SW1
!
enable password 7 08026F6028
!
```

## Configure secure, encrpytion type 5 priveleged EXEC mode password

```
SW1(config)#enable secret Cisco
```

```
SW1(config)#do sh run
Building configuration...

Current configuration : 1152 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname SW1
!
enable secret 5 $1$mERr$YlCkLMcTYWwkF1Ccndtll.
enable password 7 08026F6028
!
```

## Enter enable mode using configured secret and save
```
SW1>enable
Password:
SW1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

