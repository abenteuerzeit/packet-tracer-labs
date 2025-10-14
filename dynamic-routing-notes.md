# Dynamic Routing - Organized Notes

## Overview

Dynamic routing allows routers to adapt to network changes by automatically calculating new routes. Dynamic routing protocols automate routing table updates.

### Dynamic vs Static Routing

**Dynamic Routing Advantages:**
- Adaptability and network resilience with automatic failure recovery and minimal downtime
- Scalable support for very large and complex networks

**Static Routing Advantages:**
- Predictability and control through manually configured routes
- Useful when you need to control the exact packet path

---

## Routing Protocol Categories

Routing protocols are divided into two main categories, further classified by algorithm type:

### 1. Interior Gateway Protocols (IGP)
Used to exchange routing information within a single autonomous system (AS).

| Algorithm Type | Protocols |
|----------------|-----------|
| **Distance Vector** | RIP, EIGRP |
| **Link State** | OSPF, IS-IS |

### 2. Exterior Gateway Protocols (EGP)
Used to exchange routing information between different autonomous systems.

| Algorithm Type | Protocols |
|----------------|-----------|
| **Path Vector** | BGP |

---

## Protocol Types and Characteristics

### Distance Vector Protocols

**Key Characteristic:** Each router doesn't have a complete network map. For each destination, it only knows the metric and next-hop router. Sometimes called "routing by rumor."

#### Routing Information Protocol (RIP)
- Simple protocol
- Best for small networks or labs
- Uses hop count as metric

#### Enhanced Interior Gateway Routing Protocol (EIGRP)
- Cisco proprietary (RFC 7868)
- Also known as "Advanced distance-vector protocol"
- Suitable for large-scale networks
- Advertises using metrics to reach networks

**Key Terms:**
- **Advertisement:** Process of sharing known network information between routers
- **Metric:** Measures route efficiency to a destination network (lower is better)

### Link-State Protocols

**Key Characteristic:** Each router creates a complete network connectivity map.

**Process:**
1. Routers advertise their connected link states
2. Information stored in Link-State Database (LSDB)
3. Routes calculated from complete network view
4. CPU-intensive and memory-consuming
5. Networks divided into areas for scalability

#### Open Shortest Path First (OSPF)
- Common enterprise protocol
- Uses bandwidth-based cost metric

#### Intermediate System to Intermediate System (IS-IS)
- Typically used by Internet Service Providers

### Exterior Gateway Protocols

#### Border Gateway Protocol (BGP)
- Uses path-vector algorithm
- Routes based on series of autonomous systems
- Path-vector logic is AS-to-AS, not router-to-router
- Complex route selection process

---

## Route Types in Routing Tables

| Route Type | Protocol | Description |
|------------|----------|-------------|
| **D** | EIGRP | Routes learned using Diffusing Update Algorithm (DUAL) |
| **R** | RIP | Routes learned via RIP |
| **O** | OSPF | Routes learned via OSPF |

---

## Route Selection

Route selection has two distinct meanings:

### 1. Routing Table Population
Selecting which routes enter the routing table.

**Selection Criteria:**
- **Metric:** Compares routes to the same destination learned via the same protocol
- **Administrative Distance (AD):** Compares routes to the same destination learned via different protocols

### 2. Packet Forwarding
Selecting the best route from the routing table to forward a packet.

**Selection Rule:** Most specific matching route (longest prefix match)
- AD and metric values are NOT considered during forwarding
- Only routes in the routing table are candidates

---

## Metrics by Protocol

Each IGP uses a different metric to evaluate routes:

| Protocol | Metric | Description |
|----------|--------|-------------|
| **RIP** | Hop count | Total number of routers in the path |
| **OSPF** | Link bandwidth cost | Total cost based on link bandwidth |
| **EIGRP** | Bandwidth + Delay | Complex formula (bandwidth and delay by default) |
| **BGP** | Various factors | Complicated route selection process |
| **Static** | 0 | Always zero |

**Important:** Metrics from different protocols cannot be directly compared.

---

## Administrative Distance (AD)

AD indicates routing protocol trustworthiness. Lower AD is more preferred.

| Route Type / Protocol | Default AD |
|----------------------|------------|
| Connected | 0 |
| Static | 1 |
| External BGP (EBGP) | 20 |
| EIGRP | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| Internal BGP (IBGP) | 200 |
| Unusable route | 255 |

### Reading Routing Table Entries

```
R1# show ip route
O     192.168.3.0/24 [110/50] via 192.168.1.6, 00:08:35, GigabitEthernet0/0
                      [AD/Metric]

D     10.0.0.0 [90/3584] via 192.168.12.2, 00:33:30, GigabitEthernet0/0
               [AD/Metric]
```

---

## Equal-Cost Multi-Path (ECMP) Routing

When multiple routes to the same destination have the same metric:
- All routes are added to the routing table
- Traffic is load-balanced across all routes
- Default maximum: 4 routes per destination

---

## Floating Static Routes

A backup route configuration technique.

**Characteristics:**
- Static route with AD > 1
- Less preferred than dynamic routes
- Acts as backup if main route fails

**Configuration:**
```
R1(config)# ip route 10.0.0.0 255.255.255.0 192.168.1.2 [AD]
```

**Important:** Use AD higher than the dynamic route. If AD equals the dynamic route, the static route is still preferred.

---

## Most Specific Route Selection

**Forwarding Algorithm:**
1. For each route in the table:
   - Compare first N bits of destination IP to route network (N = prefix length)
   - If they match, route is a candidate
2. Among all matching routes:
   - Select the route with the LONGEST prefix length
   - Longer prefix = more specific route
3. Use selected route's next-hop to forward the packet

**Note:** Routes with the same network address but different prefix lengths are considered different destinations. All will be inserted into the routing table (e.g., 192.168.0.0/16, 192.168.0.0/17, 192.168.0.0/18).

---

## OSPF Configuration

### Entering OSPF Router Configuration Mode

```
R1(config)# router ospf process-id
R1(config-router)#
```

The process-id is locally significant and doesn't need to match other routers.

### The Network Command

**Purpose:** Activates the routing protocol on router interfaces.

**Syntax:**
```
network ip-address wildcard-mask area area-id
```

**Example:**
```
R1(config)# router ospf 1
R1(config-router)# network 192.168.1.0 0.0.0.3 area 0
R1(config-router)# network 192.168.1.4 0.0.0.3 area 0
R1(config-router)# network 192.168.2.0 0.0.0.255 area 0
```

**What the Network Command Does:**
1. Looks for interfaces with an IP address in the specified range
2. Activates the routing protocol on those interfaces
3. Advertises the network prefix of the interface(s) to neighbors

**Important:** The network command determines which interfaces OSPF is activated on, NOT which prefixes are advertised. The router advertises the actual interface prefixes.

---

## Wildcard Masks

A 32-bit value that specifies which bits must match between two IP addresses.

**Rules:**
- **0 bit:** Bits must match
- **1 bit:** Bits don't need to match

**Comparison with Netmasks:**
- Netmask: Defines network and host portions of an IP address
- Wildcard mask: Defines a range of IP addresses (not necessarily in same subnet)
- All 1 bits in a netmask become 0 bits in the equivalent wildcard mask (and vice versa)

**Example:** /24 netmask (255.255.255.0) = wildcard mask (0.0.0.255)

### Common Wildcard Mask Values

| Binary | Decimal |
|--------|---------|
| 0000 0000 | 0 |
| 0000 0001 | 1 |
| 0000 0011 | 3 |
| 0000 0111 | 7 |
| 0000 1111 | 15 |
| 0001 1111 | 31 |
| 0011 1111 | 63 |
| 0111 1111 | 127 |
| 1111 1111 | 255 |

### Best Practices

**Recommended:** Specify exact interface IP address with 0.0.0.0 wildcard mask
```
network 192.168.1.1 0.0.0.0 area 0
```
This ensures OSPF activates only on the intended interface.

**Lab Shortcut (not recommended for production):**
```
network 0.0.0.0 255.255.255.255 area 0
```
This activates OSPF on all interfaces.

### Wildcard Mask Flexibility

The network command is flexible. The IP address and wildcard mask don't need to match the interface's IP address and netmask exactly. As long as the appropriate bits match (as specified by the wildcard mask), OSPF will activate on the interface.