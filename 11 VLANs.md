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
