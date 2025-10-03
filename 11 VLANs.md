# VLANs

## Part 1

### Configure the correct IP address/subnet mask on each PC. Set the gateway address as the LAST USABLE address of the subnet.

### VLAN10: Engineering 10.0.0.0/26

|           | Binary                                | DDN             |
|-----------|---------------------------------------|-----------------|
| Broadcast | `00001010.00000000.00000000.00111111` | 10.0.0.63       |
| Mask      | `11111111.11111111.11111111.11000000` | 255.255.255.192 |

- PC1: 10.0.0.1 255.255.255.192
- PC2: 10.0.0.2 255.255.255.192
- Default Gateway: 10.0.0.62

### VLAN20: HR 10.0.0.64/26

|           | Binary                                | DDN             |
|-----------|---------------------------------------|-----------------|
| Broadcast | `00001010.00000000.00000000.01111111` | 10.0.0.127       |
| Mask      | `11111111.11111111.11111111.11000000` | 255.255.255.192 |

- PC3: 10.0.0.65 255.255.255.192
- PC4: 10.0.0.66 255.255.255.192
- Default Gateway: 10.0.0.126

### VLAN30: Sales 10.0.0.128/26

|           | Binary                                | DDN             |
|-----------|---------------------------------------|-----------------|
| Broadcast | `00001010.00000000.00000000.10111111` | 10.0.0.191      |
| Mask      | `11111111.11111111.11111111.11000000` | 255.255.255.192 |

- PC5: 10.0.0.129 255.255.255.192
- PC6: 10.0.0.130 255.255.255.192
- Default Gateway: 10.0.0.190

### Make three connections between R1 and SW1. Configure one interface on R1 for each VLAN. 

> Make sure the IP addresses are the gateway address you configured on the PCs.

- SW1 Gi0/1, R1 Gi0/0: VLAN10
- SW1 Gi1/1, R1 Gi0/1: VLAN20
- SW1 Gi2/1, R1 Gi0/2: VLAN30

```
R1(config)#int gi0/0
R1(config-if)#desc Engineering
R1(config-if)#ip addr 10.0.0.62 255.255.255.192
R1(config-if)#no shut

R1(config-if)#int g0/1
R1(config-if)#desc HR
R1(config-if)#ip addr 10.0.0.126 255.255.255.192
R1(config-if)#no shut

R1(config-if)#int g0/2
R1(config-if)#desc Sales
R1(config-if)#ip addr 10.0.0.190 255.255.255.192
R1(config-if)#no shut
```

### Configure SW1's interfaces in the proper VLANs. 

> Remember the interfaces that connect to R1! 
> Name the VLANs (Engeering, HR, Sales)

```
SW1(config-if)#vlan 10
SW1(config-vlan)#name Engineering
SW1(config-vlan)#vlan 20
SW1(config-vlan)#name HR
SW1(config-vlan)#vlan 30
SW1(config-vlan)#name Sales
SW1(config-vlan)#exit

SW1(config)#int g0/1
SW1(config-if)#switchport mode access 
SW1(config-if)#switchport access vlan 10

SW1(config-if)#int g1/1
SW1(config-if)#switchport mode access 
SW1(config-if)#switchport access vlan 20

SW1(config-if)#int g2/1
SW1(config-if)#switchport mode access 
SW1(config-if)#switchport access vlan 30

SW1(config-if)#int r f3/1, f4/1
SW1(config-if-range)#switchport mode access 
SW1(config-if-range)#switchport access vlan 10
SW1(config-if-range)#int r f5/1, f6/1
SW1(config-if-range)#switchport mode access 
SW1(config-if-range)#switchport access vlan 20
SW1(config-if-range)#int r f7/1, f8/1
SW1(config-if-range)#switchport mode access 
SW1(config-if-range)#switchport access vlan 30
SW1(config-if-range)#do show vlan b

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa9/1
10   Engineering                      active    Gig0/1, Fa3/1, Fa4/1
20   HR                               active    Gig1/1, Fa5/1, Fa6/1
30   Sales                            active    Gig2/1, Fa7/1, Fa8/1
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
SW1(config-if-range)#
```

### Ping between the PCs to check connectivity. 

> Send a broadcast ping from a PC (ping the subnet broadcast address), and see which PCs devices receive the broadcast (use Packet Tracer's 'Simulation Mode').


### PC1
```
C:\>ping 10.0.0.63

Pinging 10.0.0.63 with 32 bytes of data:

Request timed out.
Reply from 10.0.0.2: bytes=32 time=5ms TTL=128
Reply from 10.0.0.62: bytes=32 time=4ms TTL=255
Reply from 10.0.0.2: bytes=32 time=5ms TTL=128
Reply from 10.0.0.2: bytes=32 time=2ms TTL=128
Reply from 10.0.0.62: bytes=32 time=88ms TTL=255

Ping statistics for 10.0.0.63:
    Packets: Sent = 4, Received = 5, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 2ms, Maximum = 88ms, Average = 20ms
```

### PC5 

```
C:\>ping 10.0.0.191

Pinging 10.0.0.191 with 32 bytes of data:

Reply from 10.0.0.130: bytes=32 time=1ms TTL=128
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128
Reply from 10.0.0.190: bytes=32 time=27ms TTL=255
Reply from 10.0.0.130: bytes=32 time=27ms TTL=128

Ping statistics for 10.0.0.191:
    Packets: Sent = 4, Received = 7, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 27ms, Average = 7ms

C:\>ping 10.0.0.127

Pinging 10.0.0.127 with 32 bytes of data:

Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.190: bytes=32 time=23ms TTL=255
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255

Ping statistics for 10.0.0.127:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 23ms, Average = 5ms

C:\>ping 10.0.0.63

Pinging 10.0.0.63 with 32 bytes of data:

Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255

Ping statistics for 10.0.0.63:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.1

Pinging 10.0.0.1 with 32 bytes of data:

Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 0.0.0.0

Pinging 0.0.0.0 with 32 bytes of data:

Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128
Reply from 10.0.0.190: bytes=32 time<1ms TTL=255
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128

Ping statistics for 0.0.0.0:
    Packets: Sent = 4, Received = 8, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

### PC4

```
C:\>ping 10.0.0.2

Pinging 10.0.0.2 with 32 bytes of data:

Reply from 10.0.0.2: bytes=32 time=33ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127
Reply from 10.0.0.2: bytes=32 time=23ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 33ms, Average = 14ms

C:\>ping 10.0.0.129

Pinging 10.0.0.129 with 32 bytes of data:

Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time=1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.129:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>ping 10.0.0.127

Pinging 10.0.0.127 with 32 bytes of data:

Reply from 10.0.0.126: bytes=32 time<1ms TTL=255
Reply from 10.0.0.65: bytes=32 time<1ms TTL=128
Reply from 10.0.0.126: bytes=32 time<1ms TTL=255
Reply from 10.0.0.65: bytes=32 time<1ms TTL=128
Reply from 10.0.0.126: bytes=32 time<1ms TTL=255
Reply from 10.0.0.65: bytes=32 time<1ms TTL=128
Reply from 10.0.0.126: bytes=32 time<1ms TTL=255
Reply from 10.0.0.65: bytes=32 time<1ms TTL=128

Ping statistics for 10.0.0.127:
    Packets: Sent = 4, Received = 8, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

## Part 2

### Configure the switch interfaces connected to PCs as access ports in the correct VLAN.

```
SW1(config)#int r f0/1-2
SW1(config-if-range)#switchport access vlan 10
% Access VLAN does not exist. Creating vlan 10
SW1(config-if-range)#switchport mode access 
SW1(config-if-range)#int r f0/3-4
SW1(config-if-range)#switchport access vlan 30
% Access VLAN does not exist. Creating vlan 30
SW1(config-if-range)#switchport mode access 
SW1(config-if-range)#do show int st
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    10         auto    auto  10/100BaseTX
Fa0/2                        connected    10         auto    auto  10/100BaseTX
Fa0/3                        connected    30         auto    auto  10/100BaseTX
Fa0/4                        connected    30         auto    auto  10/100BaseTX
Fa0/5                        notconnect   1          auto    auto  10/100BaseTX
Fa0/6                        notconnect   1          auto    auto  10/100BaseTX
Fa0/7                        notconnect   1          auto    auto  10/100BaseTX
Fa0/8                        notconnect   1          auto    auto  10/100BaseTX
Fa0/9                        notconnect   1          auto    auto  10/100BaseTX
Fa0/10                       notconnect   1          auto    auto  10/100BaseTX
Fa0/11                       notconnect   1          auto    auto  10/100BaseTX
Fa0/12                       notconnect   1          auto    auto  10/100BaseTX
Fa0/13                       notconnect   1          auto    auto  10/100BaseTX
Fa0/14                       notconnect   1          auto    auto  10/100BaseTX
Fa0/15                       notconnect   1          auto    auto  10/100BaseTX
Fa0/16                       notconnect   1          auto    auto  10/100BaseTX
Fa0/17                       notconnect   1          auto    auto  10/100BaseTX
Fa0/18                       notconnect   1          auto    auto  10/100BaseTX
Fa0/19                       notconnect   1          auto    auto  10/100BaseTX
Fa0/20                       notconnect   1          auto    auto  10/100BaseTX
Fa0/21                       notconnect   1          auto    auto  10/100BaseTX

SW1(config-if-range)#^Z
%SYS-5-CONFIG_I: Configured from console by console
```

```
SW2(config)#int r f0/2-3
SW2(config-if-range)#switchport mode access 
SW2(config-if-range)#switchport access vlan 10
% Access VLAN does not exist. Creating vlan 10
SW2(config-if-range)#int f0/1
SW2(config-if)#switchport mode access 
SW2(config-if)#switchport access vlan 20
% Access VLAN does not exist. Creating vlan 20
SW2(config-if)#end
SW2#
%SYS-5-CONFIG_I: Configured from console by console

SW2#show int st
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    20         auto    auto  10/100BaseTX
Fa0/2                        connected    10         auto    auto  10/100BaseTX
Fa0/3                        connected    10         auto    auto  10/100BaseTX
Fa0/4                        notconnect   1          auto    auto  10/100BaseTX
Fa0/5                        notconnect   1          auto    auto  10/100BaseTX
Fa0/6                        notconnect   1          auto    auto  10/100BaseTX
Fa0/7                        notconnect   1          auto    auto  10/100BaseTX
Fa0/8                        notconnect   1          auto    auto  10/100BaseTX
Fa0/9                        notconnect   1          auto    auto  10/100BaseTX
Fa0/10                       notconnect   1          auto    auto  10/100BaseTX
Fa0/11                       notconnect   1          auto    auto  10/100BaseTX
Fa0/12                       notconnect   1          auto    auto  10/100BaseTX
Fa0/13                       notconnect   1          auto    auto  10/100BaseTX
Fa0/14                       notconnect   1          auto    auto  10/100BaseTX
Fa0/15                       notconnect   1          auto    auto  10/100BaseTX
Fa0/16                       notconnect   1          auto    auto  10/100BaseTX
Fa0/17                       notconnect   1          auto    auto  10/100BaseTX
Fa0/18                       notconnect   1          auto    auto  10/100BaseTX
Fa0/19                       notconnect   1          auto    auto  10/100BaseTX
Fa0/20                       notconnect   1          auto    auto  10/100BaseTX
Fa0/21                       notconnect   1          auto    auto  10/100BaseTX
Fa0/22                       notconnect   1          auto    auto  10/100BaseTX
Fa0/23                       notconnect   1          auto    auto  10/100BaseTX
Fa0/24                       notconnect   1          auto    auto  10/100BaseTX
Gig0/1                       connected    1          auto    auto  10/100BaseTX
Gig0/2                       notconnect   1          auto    auto  10/100BaseTX

SW2#show vlan b

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/4, Fa0/5, Fa0/6, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
10   VLAN0010                         active    Fa0/2, Fa0/3
20   VLAN0020                         active    Fa0/1
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active   
```

### Configure the connection between SW1 and SW2 as a trunk, allowing only the necessary VLANs.

```
SW1(config)#int g0/1
SW1(config-if)#switchport mode trunk
SW1(config-if)#switchport trunk allowed vlan 10,30
SW1#show interfaces trunk 
Port        Mode         Encapsulation  Status        Native vlan
Gig0/1      on           802.1q         trunking      1

Port        Vlans allowed on trunk
Gig0/1      10,30

Port        Vlans allowed and active in management domain
Gig0/1      10,30

Port        Vlans in spanning tree forwarding state and not pruned
Gig0/1      10,30
```

```
SW2(config)#int g0/1
SW2(config-if)#switchport mode trunk
SW2(config-if)#switchport trunk allowed vlan 10,30
SW2(config-if)#do show int tr
Port        Mode         Encapsulation  Status        Native vlan
Gig0/1      on           802.1q         trunking      1

Port        Vlans allowed on trunk
Gig0/1      10,30

Port        Vlans allowed and active in management domain
Gig0/1      10

Port        Vlans in spanning tree forwarding state and not pruned
Gig0/1      10

```

### Configure an unused VLAN as the native VLAN.

> **Make sure all necessary VLANs exist on each switch**

```
SW1(config)#int g0/1
SW1(config-if)#switchport trunk native vlan 404
SW1(config-if)#vlan 404
SW1(config-vlan)#name unused
SW1(config-vlan)#do show vlan b

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/2
10   VLAN0010                         active    Fa0/1, Fa0/2
30   VLAN0030                         active    Fa0/3, Fa0/4
404  unused                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active
```

```
SW2(config)#vlan 404
SW2(config-vlan)#name unused
SW2(config-vlan)#int g0/1
SW2(config-if)#switchport trunk native vlan 404
SW2(config-if)#do show vlan b

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/4, Fa0/5, Fa0/6, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/2
10   VLAN0010                         active    Fa0/2, Fa0/3
20   VLAN0020                         active    Fa0/1
404  unused                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active
SW2(config)#vlan 30
SW2(config-vlan)#exit
SW2(config)#do show vlan b

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/4, Fa0/5, Fa0/6, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24
10   VLAN0010                         active    Fa0/2, Fa0/3
20   VLAN0020                         active    Fa0/1
30   VLAN0030                         active    
404  unused                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active
```

### Configure the connection between SW2 and R1 using 'router on a stick'. Assign the last usable address of each subnet to R1's subinterfaces.

```
SW2(config-if)#switchport mode trunk
SW2(config-if)#switchport trunk allowed vlan 10,20,30
SW2(config-if)#switchport trunk native vlan 404
SW2#show interfaces trunk 
Port        Mode         Encapsulation  Status        Native vlan
Gig0/1      on           802.1q         trunking      404
Gig0/2      on           802.1q         trunking      404

Port        Vlans allowed on trunk
Gig0/1      10,30
Gig0/2      10,20,30

Port        Vlans allowed and active in management domain
Gig0/1      10
Gig0/2      10,20

Port        Vlans in spanning tree forwarding state and not pruned
Gig0/1      10
Gig0/2      10,20
```

1. 10.0.0.0/26 last usable address is 10.0.0.62 255.255.255.192
2. 10.0.0.64/26 last usable address is 10.0.0.126 255.255.255.192
3. 10.0.0.128/26 last usable address is 10.0.0.190 255.255.255.192

```
R1(config-if)#int g0/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip addr 10.0.0.62 255.255.255.192
R1(config-subif)#int g0/0.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 10.0.0.126 255.255.255.192
R1(config-subif)#int g0/0.30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip addr 10.0.0.190 255.255.255.192
R1(config-subif)#int g0/0
R1(config-if)#no shut
```

```
R1#show run
Building configuration...

Current configuration : 978 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!
!
!
!
!
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX152425PG-
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.0.0.62 255.255.255.192
!
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.0.0.126 255.255.255.192
!
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.0.0.190 255.255.255.192
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface GigabitEthernet0/2
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
!
!
no cdp run
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
!
!
end
```

```
R1#show ip interface b
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  up                    up 
GigabitEthernet0/0.10  10.0.0.62       YES manual up                    up 
GigabitEthernet0/0.20  10.0.0.126      YES manual up                    up 
GigabitEthernet0/0.30  10.0.0.190      YES manual up                    up 
GigabitEthernet0/1     unassigned      YES unset  administratively down down 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
```

```
R1#show ip int GigabitEthernet 0/0.10
GigabitEthernet0/0.10 is up, line protocol is up (connected)
  Internet address is 10.0.0.62/26
  Broadcast address is 255.255.255.255
  Address determined by setup command
  MTU is 1500 bytes
  Helper address is not set
  Directed broadcast forwarding is disabled
  Outgoing access list is not set
  Inbound  access list is not set
  Proxy ARP is enabled
  Security level is default
  Split horizon is enabled
  ICMP redirects are always sent
  ICMP unreachables are always sent
  ICMP mask replies are never sent
  IP fast switching is disabled
  IP fast switching on the same interface is disabled
  IP Flow switching is disabled
  IP Fast switching turbo vector
  IP multicast fast switching is disabled
  IP multicast distributed fast switching is disabled
  Router Discovery is disabled
  IP output packet accounting is disabled
  IP access violation accounting is disabled
  TCP/IP header compression is disabled
  RTP/IP header compression is disabled
  Probe proxy name replies are disabled
  Policy routing is disabled
  Network address translation is disabled
  BGP Policy Mapping is disabled
  Input features: MCI Check
  WCCP Redirect outbound is disabled
  WCCP Redirect inbound is disabled
  WCCP Redirect exclude is disabled
```

### Test connectivity by pinging between PCs.  All PCs should be able to reach each other.

#### PC2

```
C:\>ping 10.0.0.1

Pinging 10.0.0.1 with 32 bytes of data:

Reply from 10.0.0.1: bytes=32 time<1ms TTL=128
Reply from 10.0.0.1: bytes=32 time<1ms TTL=128
Reply from 10.0.0.1: bytes=32 time<1ms TTL=128
Reply from 10.0.0.1: bytes=32 time<1ms TTL=128

Ping statistics for 10.0.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.3

Pinging 10.0.0.3 with 32 bytes of data:

Reply from 10.0.0.3: bytes=32 time=1ms TTL=128
Reply from 10.0.0.3: bytes=32 time<1ms TTL=128
Reply from 10.0.0.3: bytes=32 time<1ms TTL=128
Reply from 10.0.0.3: bytes=32 time<1ms TTL=128

Ping statistics for 10.0.0.3:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>ping 10.0.0.4

Pinging 10.0.0.4 with 32 bytes of data:

Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

Ping statistics for 10.0.0.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.65

Pinging 10.0.0.65 with 32 bytes of data:

Reply from 10.0.0.65: bytes=32 time<1ms TTL=127
Reply from 10.0.0.65: bytes=32 time<1ms TTL=127
Reply from 10.0.0.65: bytes=32 time<1ms TTL=127
Reply from 10.0.0.65: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.65:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.129

Pinging 10.0.0.129 with 32 bytes of data:

Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.129:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.130

Pinging 10.0.0.130 with 32 bytes of data:

Reply from 10.0.0.130: bytes=32 time<1ms TTL=127
Reply from 10.0.0.130: bytes=32 time<1ms TTL=127
Reply from 10.0.0.130: bytes=32 time<1ms TTL=127
Reply from 10.0.0.130: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.130:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

#### PC5

```
C:\>ping 10.0.0.1

Pinging 10.0.0.1 with 32 bytes of data:

Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.2

Pinging 10.0.0.2 with 32 bytes of data:

Reply from 10.0.0.2: bytes=32 time<1ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.3

Pinging 10.0.0.3 with 32 bytes of data:

Request timed out.
Reply from 10.0.0.3: bytes=32 time=1ms TTL=127
Reply from 10.0.0.3: bytes=32 time<1ms TTL=127
Reply from 10.0.0.3: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.3:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>ping 10.0.0.4

Pinging 10.0.0.4 with 32 bytes of data:

Request timed out.
Reply from 10.0.0.4: bytes=32 time<1ms TTL=127
Reply from 10.0.0.4: bytes=32 time<1ms TTL=127
Reply from 10.0.0.4: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.4:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.129

Pinging 10.0.0.129 with 32 bytes of data:

Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127
Reply from 10.0.0.129: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.129:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.130

Pinging 10.0.0.130 with 32 bytes of data:

Reply from 10.0.0.130: bytes=32 time<1ms TTL=127
Reply from 10.0.0.130: bytes=32 time=19ms TTL=127
Reply from 10.0.0.130: bytes=32 time<1ms TTL=127
Reply from 10.0.0.130: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.130:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 19ms, Average = 4ms

```

#### PC3

```
C:\>ping 10.0.0.1

Pinging 10.0.0.1 with 32 bytes of data:

Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127
Reply from 10.0.0.1: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.2

Pinging 10.0.0.2 with 32 bytes of data:

Reply from 10.0.0.2: bytes=32 time<1ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127
Reply from 10.0.0.2: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.3

Pinging 10.0.0.3 with 32 bytes of data:

Reply from 10.0.0.3: bytes=32 time<1ms TTL=127
Reply from 10.0.0.3: bytes=32 time<1ms TTL=127
Reply from 10.0.0.3: bytes=32 time<1ms TTL=127
Reply from 10.0.0.3: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.3:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.4

Pinging 10.0.0.4 with 32 bytes of data:

Reply from 10.0.0.4: bytes=32 time<1ms TTL=127
Reply from 10.0.0.4: bytes=32 time=1ms TTL=127
Reply from 10.0.0.4: bytes=32 time<1ms TTL=127
Reply from 10.0.0.4: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>ping 10.0.0.65

Pinging 10.0.0.65 with 32 bytes of data:

Reply from 10.0.0.65: bytes=32 time<1ms TTL=127
Reply from 10.0.0.65: bytes=32 time<1ms TTL=127
Reply from 10.0.0.65: bytes=32 time<1ms TTL=127
Reply from 10.0.0.65: bytes=32 time<1ms TTL=127

Ping statistics for 10.0.0.65:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 10.0.0.130

Pinging 10.0.0.130 with 32 bytes of data:

Reply from 10.0.0.130: bytes=32 time<1ms TTL=128
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128
Reply from 10.0.0.130: bytes=32 time=1ms TTL=128
Reply from 10.0.0.130: bytes=32 time<1ms TTL=128

Ping statistics for 10.0.0.130:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```
