# Dynamic Routing

## Part 1: Fundamentals

### Overview

Dynamic routing allows routers to automatically build and update their routing tables by exchanging information with neighbor routers. This contrasts with static routing, where administrators manually configure all routes.

### Dynamic vs Static Routing

**Dynamic Routing Advantages:**
- Automatic adaptation to network topology changes
- Minimal downtime during link failures
- Scalable for large, complex networks

**Static Routing Advantages:**
- Predictable, deterministic routing paths
- Fine-grained administrative control
- Lower CPU and memory overhead

**Best Practice:** Both are often used together in real networks.

---

## Part 2: Routing Protocol Classification

### Two Main Categories

Routing protocols divide into two levels based on routing scope:

**Interior Gateway Protocols (IGP):** Exchange routing information within a single Autonomous System (AS)

**Exterior Gateway Protocols (EGP):** Exchange routing information between different Autonomous Systems

### IGP Algorithm Types

IGPs are further classified by how they calculate and share routes:

#### Distance Vector (Routing by Rumor)

**How it works:** Each router knows only what its neighbors tell it about reachable networks. The router doesn't have a complete network map—just the metric and next-hop for each destination.

**Protocols:**
- RIP (Routing Information Protocol)
- EIGRP (Enhanced Interior Gateway Routing Protocol)

#### Link State

**How it works:** Each router builds a complete connectivity map of the network by collecting link-state information from all routers. Routes are calculated from this complete topology view.

**Process:**
1. Routers advertise their connected links
2. Link information stored in Link-State Database (LSDB)
3. Shortest path calculated from complete network view
4. More CPU and memory intensive than distance vector

**Protocols:**
- OSPF (Open Shortest Path First)
- IS-IS (Intermediate System to Intermediate System)

#### Path Vector (EGP)

**How it works:** Routes are calculated using AS-to-AS logic rather than router-to-router. Focuses on the path of autonomous systems rather than individual routers. Designed for larger-scale routing.

**Protocols:**
- BGP (Border Gateway Protocol) — the only EGP in common use today

---

## Part 3: Core Concepts

### Metrics

A metric measures route efficiency and is used to select among multiple routes to the same destination learned via the **same protocol**.

**Important Rule:** Metrics from different protocols cannot be compared directly. AD is used instead to compare routes from different protocols.

#### Metric Values by Protocol

| Protocol | Metric Type | Description |
|----------|------------|-------------|
| RIP | Hop count | Number of routers in path (max 15) |
| EIGRP | Bandwidth + Delay | Complex formula based on slowest link and cumulative delays |
| OSPF | Link cost | Based on link bandwidth (lower = better) |
| Static | N/A | Metric = 0 |

### Administrative Distance (AD)

AD indicates how "trustworthy" a routing protocol is—lower AD values are more preferred and indicate the router trusts the protocol to select good routes.

AD is used to select among routes to the same destination learned via **different protocols**.

#### Default AD Values

| Source | Default AD |
|--------|------------|
| Connected | 0 |
| Static | 1 |
| External BGP (eBGP) | 20 |
| EIGRP | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| Internal BGP (iBGP) | 200 |
| Unusable | 255 |

### Floating Static Routes

A static route configured with AD greater than 1 to make it less preferred. Acts as a backup route if the primary dynamic route fails.

**Configuration:**
```
ip route destination-network netmask next-hop administrative-distance
```

**Important:** If a floating static route's AD equals the dynamic route's AD, the static route is still preferred (connected routes are best).

---

## Part 4: Route Selection Process

### Two Distinct Meanings

#### 1. Routing Table Population

**Question:** Which routes get added to the routing table?

**Selection Logic:**
1. **By Metric:** Among routes to the same destination via the *same protocol*, the route with the lowest metric is selected
2. **By AD:** Among routes to the same destination via *different protocols*, the route with the lowest AD is selected
3. **ECMP:** If multiple routes to the same destination have the same metric (same protocol) or same AD and metric (different protocols), all are added to the routing table

#### 2. Packet Forwarding

**Question:** Which route is used to forward a packet?

**Selection Rule:** Most specific matching route (longest prefix match)
- Only routes in the routing table are candidates
- AD and metric values are NOT considered during forwarding
- The route with the longest prefix length (most specific match) is selected

### Equal-Cost Multi-Path (ECMP) Routing

When multiple routes to the same destination have identical metrics:
- All routes are added to the routing table
- Traffic is load-balanced across all routes
- Allows router to distribute traffic proportionally
- Default maximum: 4 routes per destination

### Most Specific Route Selection

**Forwarding algorithm:**

Given a destination IP address to route:
1. For each route in the routing table, compare the destination IP against the route's network (using the prefix length)
2. Among all matching routes, select the one with the LONGEST prefix length
3. Forward the packet using the selected route's next-hop

**Example:** If the routing table contains routes to 192.168.0.0/16, 192.168.1.0/24, and 192.168.1.0/32, a packet destined for 192.168.1.1 will use the /32 route (most specific).

**Important:** Routes with the same network address but different prefix lengths are considered different destinations. All will be in the routing table simultaneously (e.g., 192.168.0.0/16, 192.168.0.0/17, 192.168.0.0/18).

### Reading Routing Table Entries

```
R1# show ip route
O     192.168.3.0/24 [110/50] via 192.168.1.6, 00:08:35, GigabitEthernet0/0
                      [AD/Metric]

D     10.0.0.0 [90/3584] via 192.168.12.2, 00:33:30, GigabitEthernet0/0
               [AD/Metric]
```

The values in brackets show [Administrative Distance / Metric].

---

## Part 5: RIP (Routing Information Protocol)

### Basics

**Type:** Distance Vector IGP  
**Metric:** Hop count (maximum 15 hops; anything greater = unreachable)  
**Administrative Distance:** 120  
**Update Interval:** Every 30 seconds (periodic)  
**Industry Standard:** Open protocol (not Cisco proprietary)  
**Use Case:** Small or lab networks only

### RIPv1 vs RIPv2

| Feature | RIPv1 | RIPv2 |
|---------|-------|-------|
| **Addressing Type** | Classful | Classless |
| **CIDR/VLSM Support** | ❌ No | ✅ Yes |
| **Subnet Mask Info** | Not included in updates | Included in updates |
| **Update Method** | Broadcast 255.255.255.255 | Multicast 224.0.0.9 |
| **Backward Compatible** | N/A | With RIPv1 |

#### RIPv1 Drawbacks

- Does not include subnet mask information in updates
- Assumes default classful masks:
  - Class A → /8
  - Class B → /16
  - Class C → /24
- No support for CIDR or VLSM
- Uses broadcast flooding (reaches all network devices unnecessarily)

#### RIPv2 Improvements

- Supports VLSM and CIDR
- Includes subnet mask information in updates
- Uses multicast (224.0.0.9) instead of broadcast (more efficient)
- Backward compatible with RIPv1

### RIP Configuration

```bash
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# no auto-summary
Router(config-router)# network 10.0.0.0
Router(config-router)# network 172.16.0.0
Router(config-router)# passive-interface g2/0
Router(config-router)# default-information originate
```

#### Key Configuration Commands

| Command | Purpose |
|---------|---------|
| `version 2` | Enable RIPv2 |
| `no auto-summary` | Prevent classful auto-summarization of routes |
| `network X.X.X.X` | Activate RIP on interfaces in this range (classful matching logic) |
| `passive-interface` | Stop sending RIP updates on interfaces with no neighbors |
| `default-information originate` | Advertise default route to RIP neighbors |

#### Important RIP Concepts

- The `network` command uses classful logic to match interfaces
- RIP advertises the actual interface prefixes, not the network command itself
- Equal-Cost Load Balancing (ECMP): Up to 4 paths by default
- Can be configured with `maximum-paths` command

#### Verification Commands

| Command | Shows |
|---------|-------|
| `show ip protocols` | RIP version, timers, networks, neighbors, passive interfaces, AD, etc. |
| `distance <1-255>` | Change Administrative Distance (default 120) |
| `maximum-paths <1-32>` | Sets number of equal-cost paths for load balancing |

---

## Part 6: EIGRP (Enhanced Interior Gateway Routing Protocol)

### Basics

**Type:** Advanced Distance Vector IGP (Cisco proprietary, now partially open RFC 7868)  
**Metric:** Based on bandwidth + delay (K1=1, K3=1 by default; others=0)  
**Administrative Distance:** 90 (internal routes), 170 (external routes)  
**Multicast Address:** 224.0.0.10  
**Update Method:** Triggered/incremental updates (not periodic)  
**No hop-count limit** (unlimited network diameter)  
**Unique feature:** Unequal-cost load balancing supported

### EIGRP Advantages

- Fast convergence on network topology changes
- Supports large enterprise networks
- Unequal-cost load balancing using variance command
- Efficient bandwidth use via incremental multicast updates
- More scalable than RIP

### EIGRP Configuration

```bash
Router(config)# router eigrp 1
Router(config-router)# no auto-summary
Router(config-router)# network 10.0.0.0
Router(config-router)# network 172.16.1.0 0.0.0.15
Router(config-router)# passive-interface g2/0
Router(config-router)# eigrp router-id 1.1.1.1
```

#### Key Configuration Elements

- **`1` (AS Number):** Autonomous System number (must match on all neighboring routers for adjacency formation)
- **`no auto-summary`:** Disables classful route summarization
- **`network` command:** Activates EIGRP on interfaces in specified range
- **Wildcard masks:** Supported in EIGRP network commands (`0` = must match, `1` = can vary)
- **`passive-interface`:** Stops EIGRP hello messages on specified interface (no neighbors)
- **`eigrp router-id`:** Manually set router ID (overrides automatic selection)

### Router ID Selection (EIGRP & OSPF)

**Priority order (highest to lowest):**
1. Manually configured router ID
2. Highest loopback interface IP address
3. Highest physical interface IP address

### Loopback Interfaces

Loopback interfaces are logical interfaces that simulate internal networks:
- Each router typically assigned one for management and identification
- Remain up/up unless manually shut down
- Common practice: Assign /32 subnet mask (e.g., 1.1.1.1/32 for R1)
- Used for router identification, management purposes, and stable routing table references

**Configuration:**
```
R1(config)# interface loopback0
R1(config-if)# ip address 1.1.1.1 255.255.255.255
```

### EIGRP Metric Calculation

**Default metric formula uses:**
- K1 = 1
- K2 = 0
- K3 = 1
- K4 = 0
- K5 = 0

**Default components (K1 and K3 only):**
- **Bandwidth:** Slowest link (bottleneck) in the path
- **Delay:** Cumulative sum of delays across all links in the path

**Simplified conceptually:** Metric ≈ Bandwidth + Delay

**Important:** Higher metric value = worse route

#### How Bandwidth is Determined

The bandwidth is determined by the slowest link in the path to the destination. The delay is the cumulative (sum of) delay of all links along that path.

#### Example Metric Calculation

**Route A:**
R1 —GigabitEthernet— R2 —FastEthernet— R4 —GigabitEthernet— SW1 — PC1
- Slowest link: **100 Mbps (FastEthernet)**
- Total delay: **Sum of delays across all three links**

**Route B:**
R1 —FastEthernet— R3 —FastEthernet— R4 —GigabitEthernet— SW1 — PC1
- Slowest link: **100 Mbps (FastEthernet)**
- Total delay: **Sum of delays across all three links**

### EIGRP Key Terminology

#### Feasible Distance (FD)

A router's local best metric along a path to a destination. Includes the metric to a neighbor router plus that neighbor router's advertised distance (AD) to the destination network.

#### Reported Distance (RD) / Advertised Distance (AD)

The metric that has been calculated by the neighbor (next-hop router). This is what the neighbor advertises to us about its own distance to the destination.

#### Successor

The best route to a destination network. The route with the lowest metric (lowest FD). The route is added to the routing table and used to route packets to the destination network.

#### Feasible Successor

A backup route that meets the feasibility condition and is guaranteed loop-free. If the Advertised Distance for a nonsuccessor route is less than the Feasible Distance of the successor, the route is a feasible successor. If the AD of a route is greater than the FD of the successor, the route cannot be guaranteed to be free of loops and cannot be chosen as a feasible successor.

### Feasibility Condition

The fundamental EIGRP concept that guarantees loop-free backup routes:

```
Reported Distance (RD) < Successor's Feasible Distance (FD)
```

If this condition is met, alternate routes are loop-free and can be used for load balancing or fast failover.

### Understanding EIGRP Topology Example

```
P 192.168.4.0/24, 1 successors, FD is 28672
         via 10.0.12.2 (28672/28416), GigabitEthernet0/0  ← Successor (best route)
         via 10.0.13.2 (30976/28416), FastEthernet1/0     ← Not successor

         (feasible distance/reported distance)
                     28416 < 28672 ✓ (meets feasibility condition)
```

In this example:
- Successor route has FD = 28,672
- Alternative route has RD = 28,416
- Feasibility condition is met: 28,416 < 28,672
- Therefore, the alternative is a feasible successor (loop-free backup)

### Equal-Cost Load Balancing (ECMP)

**Default variance = 1**

When multiple routes to the same destination have equal feasible distances:
- All routes with the same (lowest) FD are inserted into the routing table
- The router distributes traffic proportionally based on route metrics
- The lowest-metric route carries slightly more traffic
- Default maximum: 4 equal-cost paths

### Unequal-Cost Load Balancing

**Variance command:** Allows routes with higher FD to participate in load balancing

```bash
Router(config-router)# variance <value>
```

**How it works:**
- Variance is a multiplier applied to the successor's FD
- Routes with FD ≤ (Successor FD × Variance) are candidates for load balancing
- Example: Variance 2 means routes up to 2× the successor's FD can be used

**Critical requirement:** Only **feasible successors** can be selected for load balancing. EIGRP will only perform unequal-cost load-balancing over feasible successor routes. If a route DOES NOT meet the feasibility requirement, it will NEVER be selected for load-balancing, regardless of the variance value.

#### Load-Balancing Behavior

When multiple feasible routes exist:
- The router distributes traffic proportionally based on route metrics
- The lowest-metric route carries slightly more traffic
- Both routes appear in the routing table when viewed with `show ip route`

#### Practical Example with Variance

For network **192.168.4.0/24**:
- Successor route via R2 has FD = 28,672
- Feasible successor via R3 has RD = 28,416
- Feasibility condition is satisfied because 28,416 < 28,672
- With default variance = 1: Only successor used (ECMP if multiple equals)
- With variance = 2: Both routes appear in routing table, enabling unequal-cost load balancing

### EIGRP Neighbor Formation

After configuration, EIGRP neighbors are established almost instantly (assuming configuration is correct).

**Verify adjacency with:**
```bash
show ip eigrp neighbors
```

This command displays:
- Neighbor IP addresses
- Interface used for adjacency
- Hold timer and uptime information

### EIGRP Routes and Topology

**View routes learned through EIGRP:**
```bash
show ip route eigrp
```
Routes marked with a `D` represent EIGRP-learned routes.

**View detailed topology information:**
```bash
show ip eigrp topology
```
Shows feasible and reported distances for each destination network.

### EIGRP Show Command Example

```bash
show ip protocols
```

Displays:
- Protocol: EIGRP (and AS number)
- K values (metric calculation parameters)
- Router ID
- Auto-summary state
- Load balancing info (default 4)
- Active networks
- Passive interfaces list
- Neighbor list
- Administrative distance (90/170)

---

## Part 7: Wildcard Masks

### Purpose

Wildcard masks specify which bits of an IP address must match and which can vary. Used with `network` commands in routing protocols (EIGRP, OSPF, etc.).

### Binary Rules

- **0 bit:** Bits must match exactly
- **1 bit:** Bits can differ (don't care bits)

**Rule example:**
- 0 → bits must match
- 1 → bits can differ

### Comparison with Netmasks

| Aspect | Netmask | Wildcard Mask |
|--------|---------|---------------|
| Purpose | Defines network and host portions of an IP address | Specifies a range of IP addresses (not necessarily in same subnet) |
| Usage | Indicates network portion | Used with routing protocol network commands |
| Example | /24 = 255.255.255.0 | /24 equivalent = 0.0.0.255 |

### Quick Calculation Method

All 1s in a netmask are 0s in the equivalent wildcard mask, and vice versa.

**Quick calculation:** 255 - subnet_mask_octet = wildcard_mask_octet

### Common Wildcard Masks

| Prefix | Netmask | Wildcard Mask | Use Case |
|--------|---------|---------------|----------|
| /32 | 255.255.255.255 | 0.0.0.0 | Single IP address |
| /31 | 255.255.255.254 | 0.0.0.1 | Two IP addresses |
| /30 | 255.255.255.252 | 0.0.0.3 | Four IP addresses |
| /28 | 255.255.255.240 | 0.0.0.15 | 16 IP addresses |
| /24 | 255.255.255.0 | 0.0.0.255 | Full class C subnet |
| /16 | 255.255.0.0 | 0.0.255.255 | Full class B subnet |
| /8 | 255.0.0.0 | 0.255.255.255 | Full class A subnet |

### Decimal to Wildcard Quick Reference

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

### Wildcard Mask Flexibility

The network command is flexible. The IP address and wildcard mask in the command don't have to be the same as the IP address and netmask of the interface. If the correct bits match between the network command's IP address and the interface's IP address (as specified by the wildcard mask), the protocol will be activated on the interface.

**Example:**
- Interface has IP: 192.168.1.5/27
- Network command: `network 192.168.1.4 0.0.0.3 area 0`
- Result: Protocol activates because the bits that matter (defined by wildcard mask) match

### Best Practices

**Production best practice:** Specify exact interface IP with /32 wildcard mask
```
network 192.168.1.5 0.0.0.0 area 0
```
Ensures protocol activates only on the intended interface.

**Lab shortcut (NOT recommended for production):**
```
network 0.0.0.0 255.255.255.255 area 0
```
Activates protocol on all interfaces (matches any IP address).

---

## Part 8: OSPF and the Network Command

### Entering OSPF Configuration Mode

```bash
Router(config)# router ospf process-id
Router(config-router)#
```

**Note:** Process-id is locally significant—doesn't need to match other routers.

### The Network Command for OSPF

**Purpose:** Activates the routing protocol on router interfaces

**Syntax:**
```
network ip-address wildcard-mask area area-id
```

**What the Network Command Does:**
1. Looks for interfaces with an IP address in the specified range
2. Activates the routing protocol on those interfaces
3. Advertises the network prefix of the interface(s) to neighbors

**Critical points:**
- The network command determines which interfaces OSPF is activated on
- The network command does NOT determine which prefixes the router advertises
- The router advertises the actual interface's prefixes

### Network Command Example

```bash
R1(config)# router ospf 1
R1(config-router)# network 192.168.1.0 0.0.0.3 area 0
R1(config-router)# network 192.168.1.4 0.0.0.3 area 0
R1(config-router)# network 192.168.2.0 0.0.0.255 area 0
```

This activates OSPF on three interfaces:
- 192.168.1.1 (or similar in that range)
- 192.168.1.5 (or similar in that range)
- 192.168.2.1 (or another address in 192.168.2.0/24)

### Network Command Flexibility

The IP address and wildcard mask of the command don't have to be the same as the IP address and netmask of the interface. If the correct bits match between the network command's IP address and the interface's IP address (as defined by the wildcard mask), OSPF will be activated on the interface.

### Best Practices for Network Commands

**Recommended approach:** Specify the interface's exact IP address and use a /32 wildcard mask (0.0.0.0) in the network command to ensure that OSPF is activated only on the intended interface.

```
network 192.168.1.1 0.0.0.0 area 0
```

**Lab shortcut (NOT recommended for production):** Use network 0.0.0.0 255.255.255.255 area 0 because this matches all possible IP addresses. This is a handy shortcut in a simulated lab, but it is not recommended in a real network.

---

## Comparison: RIP vs EIGRP

| Feature | RIP | EIGRP |
|---------|-----|-------|
| **Type** | Distance Vector | Advanced Distance Vector |
| **Metric** | Hop count | Bandwidth + Delay |
| **Max Hops** | 15 (unreachable) | None (no limit) |
| **Update Method** | Periodic (every 30s) | Triggered/incremental |
| **Protocol Standard** | Open standard | Cisco proprietary (now partially open RFC 7868) |
| **Load Balancing** | Equal-cost only | Equal + Unequal-cost |
| **Multicast Address** | 224.0.0.9 | 224.0.0.10 |
| **Administrative Distance** | 120 | 90 (internal), 170 (external) |
| **Convergence Speed** | Slow | Fast |
| **Scalability** | Small networks only | Enterprise networks |
| **Bandwidth Usage** | Inefficient (periodic flooding) | Efficient (incremental updates) |

---

## Key Concepts

**Network Command Logic:** The network command is used by RIP, EIGRP, and OSPF to activate the protocol on router interfaces. Understanding both classful and classless matching is essential. Used similarly across all three protocols.

**Auto-Summary:** Default behavior on some platforms that automatically summarizes routes to classful boundaries. Generally disabled with `no auto-summary` to ensure proper CIDR/VLSM support. Key concept used in RIP, EIGRP, and OSPF.

**Passive Interfaces:** Prevent routing protocol updates and hello messages on interfaces with no neighbors (loopback, client-facing, edge interfaces). Conserves bandwidth and CPU.

**Administrative Distance:** Lower AD values are more preferred. Used to compare routes learned from different routing protocols. Understanding AD is essential for route selection.

**Feasibility Condition (EIGRP):** The fundamental EIGRP concept that guarantees loop-free backup routes. Prerequisite for becoming a feasible successor. Only routes meeting this condition can be used for fast failover and unequal-cost load balancing.

**Unequal-Cost Load Balancing:** EIGRP's unique ability (among distance vector protocols) to load balance over routes with different metrics. Controlled by the variance multiplier. Only feasible successors can participate.

**Router ID:** Used by EIGRP and OSPF for router identification. Selected via priority: manual config > highest loopback > highest physical interface IP.

**Wildcard Masks:** Mathematical inverse of netmasks. Used in network commands for routing protocols. Essential for understanding how network commands match interfaces. Quick calculation: 255 - netmask octet = wildcard mask octet.

---

## Summary

- **RIP** is simple but outdated; limited to small lab networks with 15-hop maximum.
- **EIGRP** is faster and more scalable; supports unequal-cost load balancing (Cisco advantage).
- Learn the **logic of the network command**, **auto-summary**, **passive interfaces**, and **AD values**—used similarly in OSPF.
- Understanding **wildcard masks** and **EIGRP metrics/feasibility** will make OSPF much easier to grasp.
- The **feasibility condition** is the key to understanding EIGRP's fast convergence and advanced capabilities.
- OSPF uses the same **network command** syntax and **wildcard mask** logic as EIGRP; the concepts transfer directly.
