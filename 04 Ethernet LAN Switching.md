# PC1 Pings PC2, PC3, PC4

## Initial Conditions

* All switches’ MAC tables are empty.
* All PCs’ ARP tables are empty.

Commands to verify:

```cmd
C:\>arp -a
No ARP Entries Found
```

```
SW1#show mac address-table
SW2#show mac address-table
```

**SW1**

```
SW1#show mac address-table
          Mac Address Table
-------------------------------------------
<empty>
```

**SW2**

```
SW2#show mac address-table
          Mac Address Table
-------------------------------------------
<empty>
```


## PC1 Pings PC3

### Step 1 – ARP Discovery

* PC1 sends **broadcast ARP Request**.
* PC3 replies with **unicast ARP Reply**.
* SW1 learns PC1’s MAC.
* SW2 learns PC3’s MAC.
* Both switches forward unicast reply properly, and then learn both sides.

**PC1 ARP table:**

```cmd
C:\>arp -a
Interface: 192.168.1.1 --- 0x2
  Internet Address      Physical Address      Type
  192.168.1.3           0004.9a6e.d870        dynamic
```

**PC3 ARP table:**

```cmd
C:\>arp -a
Interface: 192.168.1.3 --- 0x2
  Internet Address      Physical Address      Type
  192.168.1.1           00d0.d3ad.9cab        dynamic
```

**Switch tables after ARP:**

**SW1**

```
SW1#show mac address-table
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    00d0.d3ad.9cab    DYNAMIC     Fa0/1   (PC1)
   1    0004.9a6e.d870    DYNAMIC     Gi0/1   (towards SW2 for PC3)
```

**SW2**

```
SW2#show mac address-table
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0004.9a6e.d870    DYNAMIC     Fa0/1   (PC3)
   1    00d0.d3ad.9cab    DYNAMIC     Gi0/1   (towards SW1 for PC1)
```

### Step 2 – ICMP Echo

* PC1 sends **Echo Request** to PC3 (unicast).
* PC3 replies with **Echo Reply** (unicast).
* No new MACs are learned.

## PC1 Pings PC2

### Step 1 – ARP Discovery

* PC1 sends **broadcast ARP Request** for 192.168.1.2.
* PC2 replies with **unicast ARP Reply**.
* Switches learn PC2’s MAC.

**PC1 ARP table:**

```cmd
C:\>arp -a
Interface: 192.168.1.1 --- 0x2
  Internet Address      Physical Address      Type
  192.168.1.2           00b0.d056.ab12        dynamic
  192.168.1.3           0004.9a6e.d870        dynamic
```

**PC2 ARP table:**

```cmd
C:\>arp -a
Interface: 192.168.1.2 --- 0x2
  Internet Address      Physical Address      Type
  192.168.1.1           00d0.d3ad.9cab        dynamic
```

**Switch tables after ARP:**

**SW1**

```
SW1#show mac address-table
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    00d0.d3ad.9cab    DYNAMIC     Fa0/1   (PC1)
   1    00b0.d056.ab12    DYNAMIC     Fa0/2   (PC2)
   1    0004.9a6e.d870    DYNAMIC     Gi0/1   (towards SW2 for PC3)
```

**SW2**

```
SW2#show mac address-table
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0004.9a6e.d870    DYNAMIC     Fa0/1   (PC3)
   1    00d0.d3ad.9cab    DYNAMIC     Gi0/1   (towards SW1 for PC1)
   1    00b0.d056.ab12    DYNAMIC     Gi0/1   (towards SW1 for PC2)
```

### Step 2 – ICMP Echo

* PC1 sends **Echo Request** to PC2.
* PC2 replies with **Echo Reply**.
* No new MACs are learned.


## PC1 Pings PC4

### Step 1 – ARP Discovery

* PC1 sends **broadcast ARP Request** for 192.168.1.4.
* PC4 replies with **unicast ARP Reply**.
* Switches learn PC4’s MAC.

**PC1 ARP table:**

```cmd
C:\>arp -a
Interface: 192.168.1.1 --- 0x2
  Internet Address      Physical Address      Type
  192.168.1.2           00b0.d056.ab12        dynamic
  192.168.1.3           0004.9a6e.d870        dynamic
  192.168.1.4           00c0.b7ef.34cd        dynamic
```

**PC4 ARP table:**

```cmd
C:\>arp -a
Interface: 192.168.1.4 --- 0x2
  Internet Address      Physical Address      Type
  192.168.1.1           00d0.d3ad.9cab        dynamic
```

**Switch tables after ARP:**

**SW1**

```
SW1#show mac address-table
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    00d0.d3ad.9cab    DYNAMIC     Fa0/1   (PC1)
   1    00b0.d056.ab12    DYNAMIC     Fa0/2   (PC2)
   1    0004.9a6e.d870    DYNAMIC     Gi0/1   (towards SW2 for PC3)
   1    00c0.b7ef.34cd    DYNAMIC     Gi0/1   (towards SW2 for PC4)
```

**SW2**

```
SW2#show mac address-table
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0004.9a6e.d870    DYNAMIC     Fa0/1   (PC3)
   1    00c0.b7ef.34cd    DYNAMIC     Fa0/2   (PC4)
   1    00d0.d3ad.9cab    DYNAMIC     Gi0/1   (towards SW1 for PC1)
   1    00b0.d056.ab12    DYNAMIC     Gi0/1   (towards SW1 for PC2)
```

### Step 2 – ICMP Echo

* PC1 sends **Echo Request** to PC4.
* PC4 replies with **Echo Reply**.
* No new MACs are learned.


## Clearing Learned Addresses

To restart the lab:

On **switches**:

```cmd
SW1#clear mac address-table dynamic
SW2#clear mac address-table dynamic
```

On **PCs**:

```cmd
C:\>arp -d *
```


## Summary

**Messages sent over the network:**

* **Broadcasts:**

  * ARP Requests from PC1 → received by all PCs and both switches.
* **Unicasts:**

  * ARP Replies from PC2, PC3, PC4 → only PC1 receives.
  * ICMP Echo Requests → unicast from PC1 to PC2, PC3, PC4.
  * ICMP Echo Replies → unicast from PC2, PC3, PC4 to PC1.

**Devices receiving them:**

* **ARP Requests:** all devices (PC2, PC3, PC4, SW1, SW2).
* **ARP Replies:** only PC1.
* **ICMP Echo Requests:** only the target PC.
* **ICMP Echo Replies:** only PC1.

