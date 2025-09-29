# Interface Configuration

## Configure the hostname of R1, SW1, and SW2

```
Router(config)#h R1
R1(config)#
```

```
Switch(config)#h SW1
SW1(config)#
```

```
Switch(config)#h SW2
SW2(config)#
```

## Configure the appropriate IP addresses on R1, PC1, PC2, PC3, PC4

```
R1(config)#do sh ip int b
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  administratively down down 
GigabitEthernet0/1     unassigned      YES unset  administratively down down 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R1(config)#int g0/0
R1(config-if)#ip addr 172.16.255.254 255.255.0.0
R1(config-if)#no shut

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

```

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::201:64FF:FE84:DDAA
   IPv6 Address....................: ::
   IPv4 Address....................: 172.16.0.1
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: ::
                                     172.16.255.254
```

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::230:F2FF:FE1C:46E3
   IPv6 Address....................: ::
   IPv4 Address....................: 172.16.0.2
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: ::
                                     172.16.255.254
```

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::203:E4FF:FE42:5E07
   IPv6 Address....................: ::
   IPv4 Address....................: 172.16.0.3
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: ::
                                     172.16.255.254
```

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2D0:97FF:FE79:1524
   IPv6 Address....................: ::
   IPv4 Address....................: 172.16.0.4
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: ::
                                     172.16.255.254
```

## Manually configure the speed and duplex on interfaces connected to other networking devices (not end hosts)

```
R1(config)#int g0/0
R1(config-if)#speed 1000
R1(config-if)#duplex full
R1(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

R1(config-if)#
```

```
SW1(config)#int g0/1
SW1(config-if)#speed 1000
SW1(config-if)#duplex full
SW1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

SW1(config-if)#int g0/2
SW1(config-if)#speed 1000
SW1(config-if)#duplex full
SW1(config-if)#
%LINK-3-UPDOWN: Interface GigabitEthernet0/2, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to down

SW1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to up
```

```
SW2(config)#int g0/2
SW2(config-if)#int g0/1
SW2(config-if)#speed 1000
SW2(config-if)#duplex full
SW2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
```

## Configure appropriate descriptions on each interface

```
R1(config-if)#description To SW1
```

```
SW1(config-if)#int g0/1
SW1(config-if)#description To R1
SW1(config-if)#int g0/2
SW1(config-if)#description to SW2
```

```
SW2(config-if)#description To SW1
```

## Disable interfaces which are not connected to other devices

```
SW1(config)#interface range Fa0/3-24
SW1(config-if-range)#shut
SW1(config-if-range)#do show int st
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    1          auto    auto  10/100BaseTX
Fa0/2                        connected    1          auto    auto  10/100BaseTX
Fa0/3                        disabled 1          auto    auto  10/100BaseTX
Fa0/4                        disabled 1          auto    auto  10/100BaseTX
Fa0/5                        disabled 1          auto    auto  10/100BaseTX
Fa0/6                        disabled 1          auto    auto  10/100BaseTX
Fa0/7                        disabled 1          auto    auto  10/100BaseTX
Fa0/8                        disabled 1          auto    auto  10/100BaseTX
Fa0/9                        disabled 1          auto    auto  10/100BaseTX
Fa0/10                       disabled 1          auto    auto  10/100BaseTX
Fa0/11                       disabled 1          auto    auto  10/100BaseTX
Fa0/12                       disabled 1          auto    auto  10/100BaseTX
Fa0/13                       disabled 1          auto    auto  10/100BaseTX
Fa0/14                       disabled 1          auto    auto  10/100BaseTX
Fa0/15                       disabled 1          auto    auto  10/100BaseTX
Fa0/16                       disabled 1          auto    auto  10/100BaseTX
Fa0/17                       disabled 1          auto    auto  10/100BaseTX
Fa0/18                       disabled 1          auto    auto  10/100BaseTX
Fa0/19                       disabled 1          auto    auto  10/100BaseTX
Fa0/20                       disabled 1          auto    auto  10/100BaseTX
Fa0/21                       disabled 1          auto    auto  10/100BaseTX
Fa0/22                       disabled 1          auto    auto  10/100BaseTX
Fa0/23                       disabled 1          auto    auto  10/100BaseTX
Fa0/24                       disabled 1          auto    auto  10/100BaseTX
Gig0/1    To R1              connected    1          a-full  a-100010/100BaseTX
Gig0/2    to SW2             connected    1          a-full  a-100010/100BaseTX
```

```
SW2(config)#int r Fa0/3-24, Gi0/2
SW2(config-if-range)#shut
SW2(config-if-range)#do show int st
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    1          auto    auto  10/100BaseTX
Fa0/2                        connected    1          auto    auto  10/100BaseTX
Fa0/3                        disabled 1          auto    auto  10/100BaseTX
Fa0/4                        disabled 1          auto    auto  10/100BaseTX
Fa0/5                        disabled 1          auto    auto  10/100BaseTX
Fa0/6                        disabled 1          auto    auto  10/100BaseTX
Fa0/7                        disabled 1          auto    auto  10/100BaseTX
Fa0/8                        disabled 1          auto    auto  10/100BaseTX
Fa0/9                        disabled 1          auto    auto  10/100BaseTX
Fa0/10                       disabled 1          auto    auto  10/100BaseTX
Fa0/11                       disabled 1          auto    auto  10/100BaseTX
Fa0/12                       disabled 1          auto    auto  10/100BaseTX
Fa0/13                       disabled 1          auto    auto  10/100BaseTX
Fa0/14                       disabled 1          auto    auto  10/100BaseTX
Fa0/15                       disabled 1          auto    auto  10/100BaseTX
Fa0/16                       disabled 1          auto    auto  10/100BaseTX
Fa0/17                       disabled 1          auto    auto  10/100BaseTX
Fa0/18                       disabled 1          auto    auto  10/100BaseTX
Fa0/19                       disabled 1          auto    auto  10/100BaseTX
Fa0/20                       disabled 1          auto    auto  10/100BaseTX
Fa0/21                       disabled 1          auto    auto  10/100BaseTX
Fa0/22                       disabled 1          auto    auto  10/100BaseTX
Fa0/23                       disabled 1          auto    auto  10/100BaseTX
Fa0/24                       disabled 1          auto    auto  10/100BaseTX
Gig0/1    To SW1             connected    1          a-full  a-100010/100BaseTX
Gig0/2                       disabled 1          auto    auto  10/100BaseTX
```
