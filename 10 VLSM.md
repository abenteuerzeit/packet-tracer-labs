# VLSM

1. Subnet the $192.168.5.0/24$ network to provide sufficient addressing for each LAN. (Also, the point-to-point connection between R1 and R2).
2. Assign the first usable address to the PC in each LAN.
3. Assign the last usable address to the router's interface in each LAN.
4. Configure static routes on each router so that all PCs can ping eachother.

## Subnets 

| LAN Id | Requirement | Netmask | Size | Network       | Broadcast       | Range                            |
|--------|-------------|---------|------|---------------|-----------------|----------------------------------|
| 2      | 64          | /25     | 126  | 192.168.5.0   | 192.168.5.127   | 192.168.5.1, 192.168.5.126      |
| 1      | 45          | /26     | 62   | 192.168.5.128 | 192.168.5.191   | 192.168.5.129, 192.168.5.190    |
| 3      | 14          | /28     | 14   | 192.168.5.192 | 192.168.5.207   | 192.168.5.193, 192.168.5.206    |
| 4      | 9           | /28     | 14   | 192.168.5.208 | 192.168.5.223   | 192.168.5.209, 192.168.5.222    |
| p2p    | 2           | /30     | 2    | 192.168.5.224 | 192.168.5.227   | 192.168.5.225, 192.168.5.226    |

## Assigned

| Description     | DDN           | Binary                                   | 
|-----------------|---------------|------------------------------------------|
| Network Address | 192.168.5.0   | `11000000.10101000.00000101.00000000`   |
| Subnet Mask     | 255.255.255.0 | `11111111.11111111.11111111.00000000`   |

$2^8 - 2 = 254$ Addresses

| Power  | $2^0$ | $2^1$ | $2^2$ | $2^3$ | $2^4$ | $2^5$ | $2^6$ | $2^7$ |
|--------|-------|-------|-------|-------|-------|-------|-------|-------|
| Value  | $1$   | $2$   | $4$   | $8$   | $16$  | $32$  | $64$  | $128$ |

## LAN 2 

For LAN 2, we need at least $64$ assignable addresses $a$

What is the lowest value of host bits $h$ that satisfies the requirement for $64$ addresses? 

$2^h - 2 \geq a$

$h = 7$

$2^7 - 2 \geq 64$

$128 - 2 \geq 64$

$126 \geq 64$

| Address Type           | DDN             | Binary                                   |
|------------------------|-----------------|------------------------------------------|
| Network Address        | 192.168.5.0     | `11000000.10101000.00000101.00000000`   |
| Subnet Mask            | 255.255.255.128 | `11111111.11111111.11111111.10000000`   |
| First Usable Address   | 192.168.5.1     | `11000000.10101000.00000101.00000001`   |
| Last Usable Address    | 192.168.5.126   | `11000000.10101000.00000101.01111110`   |
| Broadcast Address      | 192.168.5.127   | `11000000.10101000.00000101.01111111`   |

### PC2

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2E0:8FFF:FE61:DDA6
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.5.1
   Subnet Mask.....................: 255.255.255.128
   Default Gateway.................: ::
                                     192.168.5.126
```

### R1 Gi0/1

```
R1(config)#interface Gi0/1
R1(config-if)#ip address 192.168.5.126 255.255.255.128
R1(config-if)#no shut
```

## LAN 1

We now have $256 - 128 = 128$ available addresses. 

We need $6$ host bits. 

| Address Type           | DDN             | Binary                                   |
|------------------------|-----------------|------------------------------------------|
| Network Address        | 192.168.5.128   | `11000000.10101000.00000101.10000000`   |
| Subnet Mask            | 255.255.255.192 | `11111111.11111111.11111111.11000000`   |
| First Usable Address   | 192.168.5.129   | `11000000.10101000.00000101.10000001`   |
| Last Usable Address    | 192.168.5.190   | `11000000.10101000.00000101.10111110`   |
| Broadcast Address      | 192.168.5.191   | `11000000.10101000.00000101.10111111`   |

Adding the size to the last octet shows the next network address: $128 + 64 = 192$

Usable range (add/subtract one): $192.168.5.129 - 192.168.5.190$

### PC1

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::20A:41FF:FEE2:25B0
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.5.129
   Subnet Mask.....................: 255.255.255.192
   Default Gateway.................: ::
                                     192.168.5.190
```

### R1 Gi0/0

```
R1(config-if)#int Gi0/0
R1(config-if)#ip addr 192.168.5.190 255.255.255.192
R1(config-if)#no shut
```

## LAN 3

Remaining addresses: $128 - 64 = 64$

Host bits: $4$

$192 + 16 = 208$

| Address Type           | DDN             | Binary                                   |
|------------------------|-----------------|------------------------------------------|
| Network Address        | 192.168.5.192   | `11000000.10101000.00000101.11000000`   |
| Subnet Mask            | 255.255.255.240 | `11111111.11111111.11111111.11110000`   |
| First Usable Address   | 192.168.5.193   | `11000000.10101000.00000101.11000001`   |
| Last Usable Address    | 192.168.5.206   | `11000000.10101000.00000101.11001110`   |
| Broadcast Address      | 192.168.5.207   | `11000000.10101000.00000101.11001111`   |

### PC3

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::230:F2FF:FE43:77A6
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.5.193
   Subnet Mask.....................: 255.255.255.240
   Default Gateway.................: ::
                                     192.168.5.206
```

### R2 Gi0/0

```
R2(config)#int Gi0/0
R2(config-if)#ip addr 192.168.5.206 255.255.255.240
R2(config-if)#no shut
```

## LAN 4

Remaining addresses: $64 - 16 = 48$

Host bits: $4$

$208 + 16 = 224$

| Address Type           | DDN             | Binary                                   |
|------------------------|-----------------|------------------------------------------|
| Network Address        | 192.168.5.208   | `11000000.10101000.00000101.11010000`   |
| Subnet Mask            | 255.255.255.240 | `11111111.11111111.11111111.11110000`   |
| First Usable Address   | 192.168.5.209   | `11000000.10101000.00000101.11010001`   |
| Last Usable Address    | 192.168.5.222   | `11000000.10101000.00000101.11011110`   |
| Broadcast Address      | 192.168.5.223   | `11000000.10101000.00000101.11011111`   |

### PC4

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2E0:B0FF:FE10:ADB2
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.5.209
   Subnet Mask.....................: 255.255.255.240
   Default Gateway.................: ::
                                     192.168.5.222
```

### R2 Gi0/1

```
R2(config-if)#int Gi0/1
R2(config-if)#ip addr 192.168.5.222 255.255.255.240
R2(config-if)#no shut
```

## Point-to-point link

Remaining: $48 - 16 = 32$

Host bits: $2$

$224 + 4 = 228$

| Address Type           | DDN             | Binary                                   |
|------------------------|-----------------|------------------------------------------|
| Network Address        | 192.168.5.224   | `11000000.10101000.00000101.11100000`   |
| Subnet Mask            | 255.255.255.252 | `11111111.11111111.11111111.11111100`   |
| First Usable Address   | 192.168.5.225   | `11000000.10101000.00000101.11100001`   |
| Last Usable Address    | 192.168.5.226   | `11000000.10101000.00000101.11100010`   |
| Broadcast Address      | 192.168.5.227   | `11000000.10101000.00000101.11100011`   |

### R1 Gi0/0/0

```
R1(config-if)#int Gi0/0/0
R1(config-if)#ip addr 192.168.5.225 255.255.255.252
R1(config-if)#no shut
```

### R2 Gi0/0/0

```
R2(config-if)#int g0/0/0
R2(config-if)#ip addr 192.168.5.226 255.255.255.252
R2(config-if)#no shut
```

## Static Routes

### R1

```
R1(config)#ip route 192.168.5.192 255.255.255.240 192.168.5.226
R1(config)#ip route 192.168.5.208 255.255.255.240 192.168.5.226
R1(config)#do sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.5.0/24 is variably subnetted, 8 subnets, 5 masks
C       192.168.5.0/25 is directly connected, GigabitEthernet0/1
L       192.168.5.126/32 is directly connected, GigabitEthernet0/1
C       192.168.5.128/26 is directly connected, GigabitEthernet0/0
L       192.168.5.190/32 is directly connected, GigabitEthernet0/0
S       192.168.5.192/28 [1/0] via 192.168.5.226
S       192.168.5.208/28 [1/0] via 192.168.5.226
C       192.168.5.224/30 is directly connected, GigabitEthernet0/0/0
L       192.168.5.225/32 is directly connected, GigabitEthernet0/0/0
```

### R2

```
R2(config)#ip route 192.168.5.0 255.255.255.128 Gi0/0/0
%Default route without gateway, if not a point-to-point interface, may impact performance
R2(config)#ip route 192.168.5.128 255.255.255.192 Gi0/0/0
%Default route without gateway, if not a point-to-point interface, may impact performance
R2(config)#do sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.5.0/24 is variably subnetted, 8 subnets, 5 masks
S       192.168.5.0/25 is directly connected, GigabitEthernet0/0/0
S       192.168.5.128/26 is directly connected, GigabitEthernet0/0/0
C       192.168.5.192/28 is directly connected, GigabitEthernet0/0
L       192.168.5.206/32 is directly connected, GigabitEthernet0/0
C       192.168.5.208/28 is directly connected, GigabitEthernet0/1
L       192.168.5.222/32 is directly connected, GigabitEthernet0/1
C       192.168.5.224/30 is directly connected, GigabitEthernet0/0/0
L       192.168.5.226/32 is directly connected, GigabitEthernet0/0/0
```

## Connectivity Tests

### PC1 Pings

```
C:\>ping 192.168.5.1

Pinging 192.168.5.1 with 32 bytes of data:

Request timed out.
Reply from 192.168.5.1: bytes=32 time<1ms TTL=127
Reply from 192.168.5.1: bytes=32 time<1ms TTL=127
Reply from 192.168.5.1: bytes=32 time<1ms TTL=127

Ping statistics for 192.168.5.1:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.193

Pinging 192.168.5.193 with 32 bytes of data:

Request timed out.
Request timed out.
Request timed out.
Reply from 192.168.5.193: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.193:
    Packets: Sent = 4, Received = 1, Lost = 3 (75% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.209

Pinging 192.168.5.209 with 32 bytes of data:

Request timed out.
Reply from 192.168.5.209: bytes=32 time<1ms TTL=126
Reply from 192.168.5.209: bytes=32 time<1ms TTL=126
Reply from 192.168.5.209: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.209:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

### PC2 Pings

```
C:\>ping 192.168.5.129

Pinging 192.168.5.129 with 32 bytes of data:

Reply from 192.168.5.129: bytes=32 time<1ms TTL=127
Reply from 192.168.5.129: bytes=32 time<1ms TTL=127
Reply from 192.168.5.129: bytes=32 time<1ms TTL=127
Reply from 192.168.5.129: bytes=32 time<1ms TTL=127

Ping statistics for 192.168.5.129:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.193

Pinging 192.168.5.193 with 32 bytes of data:

Request timed out.
Reply from 192.168.5.193: bytes=32 time<1ms TTL=126
Reply from 192.168.5.193: bytes=32 time<1ms TTL=126
Reply from 192.168.5.193: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.193:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.209

Pinging 192.168.5.209 with 32 bytes of data:

Reply from 192.168.5.209: bytes=32 time<1ms TTL=126
Reply from 192.168.5.209: bytes=32 time<1ms TTL=126
Reply from 192.168.5.209: bytes=32 time<1ms TTL=126
Reply from 192.168.5.209: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.209:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

### PC3 Pings

```
C:\>ping 192.168.5.1

Pinging 192.168.5.1 with 32 bytes of data:

Reply from 192.168.5.1: bytes=32 time<1ms TTL=126
Reply from 192.168.5.1: bytes=32 time<1ms TTL=126
Reply from 192.168.5.1: bytes=32 time<1ms TTL=126
Reply from 192.168.5.1: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.129

Pinging 192.168.5.129 with 32 bytes of data:

Reply from 192.168.5.129: bytes=32 time<1ms TTL=126
Reply from 192.168.5.129: bytes=32 time<1ms TTL=126
Reply from 192.168.5.129: bytes=32 time<1ms TTL=126
Reply from 192.168.5.129: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.129:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.209

Pinging 192.168.5.209 with 32 bytes of data:

Reply from 192.168.5.209: bytes=32 time<1ms TTL=127
Reply from 192.168.5.209: bytes=32 time<1ms TTL=127
Reply from 192.168.5.209: bytes=32 time<1ms TTL=127
Reply from 192.168.5.209: bytes=32 time<1ms TTL=127

Ping statistics for 192.168.5.209:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

### PC4 Pings

```
C:\>ping 192.168.5.1

Pinging 192.168.5.1 with 32 bytes of data:

Reply from 192.168.5.1: bytes=32 time<1ms TTL=126
Reply from 192.168.5.1: bytes=32 time<1ms TTL=126
Reply from 192.168.5.1: bytes=32 time<1ms TTL=126
Reply from 192.168.5.1: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.129

Pinging 192.168.5.129 with 32 bytes of data:

Reply from 192.168.5.129: bytes=32 time<1ms TTL=126
Reply from 192.168.5.129: bytes=32 time<1ms TTL=126
Reply from 192.168.5.129: bytes=32 time<1ms TTL=126
Reply from 192.168.5.129: bytes=32 time<1ms TTL=126

Ping statistics for 192.168.5.129:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.5.193

Pinging 192.168.5.193 with 32 bytes of data:

Reply from 192.168.5.193: bytes=32 time<1ms TTL=127
Reply from 192.168.5.193: bytes=32 time<1ms TTL=127
Reply from 192.168.5.193: bytes=32 time<1ms TTL=127
Reply from 192.168.5.193: bytes=32 time<1ms TTL=127

Ping statistics for 192.168.5.193:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

