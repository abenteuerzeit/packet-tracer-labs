# IPv4 Addresses

## Configure R1's hostname

```
Router(config)#hostname R1
R1(config)#
```

## Use a `show` command to view a list of R1's interfaces, their IP addresses, status, etc.

```
R1#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  administratively down down 
GigabitEthernet0/1     unassigned      YES unset  administratively down down 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
```

```
R1#show interfaces GigabitEthernet 0/0
GigabitEthernet0/0 is administratively down, line protocol is down (disabled)
  Hardware is CN Gigabit Ethernet, address is 0060.3e72.3101 (bia 0060.3e72.3101)
  MTU 1500 bytes, BW 1000000 Kbit, DLY 10 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full-duplex, 100Mb/s, media type is RJ45
  output flow-control is unsupported, input flow-control is unsupported
  ARP type: ARPA, ARP Timeout 04:00:00, 
  Last input 00:00:08, output 00:00:05, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0 (size/max/drops); Total output drops: 0
  Queueing strategy: fifo
  Output queue :0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     0 packets input, 0 bytes, 0 no buffer
     Received 0 broadcasts, 0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     0 watchdog, 1017 multicast, 0 pause input
     0 input packets with dribble condition detected
     0 packets output, 0 bytes, 0 underruns
     0 output errors, 0 collisions, 2 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
```

## Configure the appropriate IP addresses on R1's interfaces, and enable the interfaces. Configure appropriate interface descriptions.

```
R1(config)#interface GigabitEthernet 0/0
R1(config-if)#ip address 15.255.255.254 255.0.0.0
R1(config-if)#description Blue
R1(config-if)#no shut
```

```
R1(config)#int g0/1
R1(config-if)#ip addr 182.98.255.254 255.255.0.0
R1(config-if)#description Green
R1(config-if)#no shut
```

```
R1(config-if)#int g0/2
R1(config-if)#ip address 201.191.20.254 255.255.255.0
R1(config-if)#no shut
R1(config-if)#description Yellow
```

## Use a `show` command to verify R1's interfaces again.

```
R1(config-if)#do show ip int b
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     15.255.255.254  YES manual up                    up 
GigabitEthernet0/1     182.98.255.254  YES manual up                    up 
GigabitEthernet0/2     201.191.20.254  YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
```

## View the running config to confirm the configuration changes, then save the config

```
R1#show running-config 
Building configuration...

Current configuration : 797 bytes
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
license udi pid CISCO2911/K9 sn FTX1524NMDP-
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
 description Blue
 ip address 15.255.255.254 255.0.0.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 description Green
 ip address 182.98.255.254 255.255.0.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/2
 description Yellow
 ip address 201.191.20.254 255.255.255.0
 duplex auto
 speed auto
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
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

## Configure the IP addresses of PC1, PC2, and PC3 (Watch the video to learn how to do this in Packet Tracer)

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0040.0B02.5A32
   Link-local IPv6 Address.........: FE80::240:BFF:FE02:5A32
   IPv6 Address....................: ::
   IPv4 Address....................: 15.0.0.1
   Subnet Mask.....................: 255.0.0.0
   Default Gateway.................: ::
                                     15.255.255.254
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-E6-E0-CC-BD-00-40-0B-02-5A-32
   DNS Servers.....................: ::
                                     0.0.0.0
```

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::240:BFF:FEBC:34C4
   IPv6 Address....................: ::
   IPv4 Address....................: 182.98.0.1
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: ::
                                     182.98.255.254
```

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::20A:F3FF:FEAA:27B0
   IPv6 Address....................: ::
   IPv4 Address....................: 201.191.20.1
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     201.191.20.254
```

## Ping from PC1 to PC2 and PC3 to test connectivity

```
C:\>ping 182.98.0.1

Pinging 182.98.0.1 with 32 bytes of data:

Request timed out.
Reply from 182.98.0.1: bytes=32 time<1ms TTL=127
Reply from 182.98.0.1: bytes=32 time<1ms TTL=127
Reply from 182.98.0.1: bytes=32 time<1ms TTL=127

Ping statistics for 182.98.0.1:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 201.191.20.1

Pinging 201.191.20.1 with 32 bytes of data:

Request timed out.
Reply from 201.191.20.1: bytes=32 time<1ms TTL=127
Reply from 201.191.20.1: bytes=32 time<1ms TTL=127
Reply from 201.191.20.1: bytes=32 time<1ms TTL=127

Ping statistics for 201.191.20.1:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

