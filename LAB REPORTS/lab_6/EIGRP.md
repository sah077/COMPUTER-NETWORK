# Lab Report: EIGRP (Enhanced Interior Gateway Routing Protocol)

**Course:** Computer Networks  
**Lab File:** EIGRP.pkt  
**Date:** March 2026  

---

## Objective

To configure and verify EIGRP on Cisco routers using Cisco Packet Tracer, observe neighbor discovery, Diffusing Update Algorithm (DUAL) operation, and verify load balancing capabilities.

---

## Background Theory

**EIGRP** (Enhanced Interior Gateway Routing Protocol) is an advanced distance-vector protocol developed by Cisco, later made open standard (RFC 7868). It combines features of both distance-vector and link-state protocols.

**Key Concepts:**

- **DUAL Algorithm:** Guarantees loop-free paths at every instant and allows fast convergence.
- **Successor:** The primary route to a destination (lowest feasible distance).
- **Feasible Successor:** A backup route that satisfies the feasibility condition (pre-computed backup path).
- **Composite Metric:** Calculated using Bandwidth, Delay, Reliability, Load, and MTU (K values).
- **Autonomous System (AS):** Routers must share the same AS number to exchange EIGRP routes.

**Default Metric Formula:**
```
Metric = [K1×Bandwidth + (K2×Bandwidth)/(256-Load) + K3×Delay] × [K5/(Reliability+K4)]
```
Default K values: K1=1, K2=0, K3=1, K4=0, K5=0 → simplifies to Bandwidth + Delay.

**EIGRP Packet Types:**

| Type   | Name   | Purpose                              |
|--------|--------|--------------------------------------|
| Hello  | 5      | Neighbor discovery and keepalive     |
| Update | 1      | Routing information exchange         |
| Query  | 3      | Request for route information        |
| Reply  | 4      | Response to Query                    |
| ACK    | 2      | Reliable delivery acknowledgment     |

---

## Network Topology

Multiple routers configured in a hub-and-spoke or full-mesh topology with EIGRP providing dynamic routing.

**Devices Used:**
- 3× Cisco Routers (Router0, Router1, Router2)
- 3× PCs (one per LAN)
- Connecting switches

**IP Address Scheme:**

| Interface         | IP Address      | Subnet Mask     |
|-------------------|-----------------|-----------------|
| R0 Fa0/0          | 192.168.1.1     | 255.255.255.0   |
| R0 Se0/0          | 172.16.0.1      | 255.255.255.252 |
| R1 Se0/0          | 172.16.0.2      | 255.255.255.252 |
| R1 Fa0/0          | 192.168.2.1     | 255.255.255.0   |
| R1 Se0/1          | 172.16.1.1      | 255.255.255.252 |
| R2 Se0/0          | 172.16.1.2      | 255.255.255.252 |
| R2 Fa0/0          | 192.168.3.1     | 255.255.255.0   |

---

## Procedure

1. Open `EIGRP.pkt` in Cisco Packet Tracer.
2. Verify all interface IP assignments.
3. Configure EIGRP on all routers:
   ```
   Router(config)# router eigrp 100
   Router(config-router)# network 192.168.1.0
   Router(config-router)# network 172.16.0.0
   Router(config-router)# no auto-summary
   ```
4. Verify neighbor table: `show ip eigrp neighbors`
5. Check topology table: `show ip eigrp topology`
6. Verify routing table: `show ip route eigrp`
7. Test connectivity with extended ping.
8. Simulate link failure and observe DUAL reconvergence.

---

## Observations

- EIGRP neighbor adjacency formed quickly (within seconds).
- The topology table showed Successor and Feasible Successor routes.
- `show ip route` displayed `D` (EIGRP) routes with AD of 90.
- When the primary link was shut down, EIGRP instantly promoted the Feasible Successor — no reconvergence delay.
- EIGRP used multicast address `224.0.0.10` for Hello and Update packets.

**Sample Topology Table Output:**
```
P 192.168.2.0/24, 1 successors, FD is 2172416
    via 172.16.0.2 (2172416/28160), Serial0/0      ← Successor
P 192.168.3.0/24, 1 successors, FD is 2684416
    via 172.16.0.2 (2684416/2172416), Serial0/0    ← Successor
```

---

## Results and Analysis

EIGRP demonstrated rapid convergence due to pre-computed backup routes (Feasible Successors). The DUAL algorithm prevented routing loops. Unequal-cost load balancing was verified using the `variance` command, distributing traffic across multiple paths proportional to their metrics.

**Comparison: EIGRP vs OSPF:**

| Feature              | EIGRP              | OSPF               |
|----------------------|--------------------|--------------------|
| Type                 | Advanced DV        | Link-State         |
| Algorithm            | DUAL               | Dijkstra SPF       |
| Admin Distance       | 90 (internal)      | 110                |
| Convergence Speed    | Very Fast          | Fast               |
| Load Balancing       | Equal + Unequal    | Equal only         |
| Vendor               | Cisco (open std)   | Open standard      |

---

## Conclusion

EIGRP proved to be an efficient and fast-converging routing protocol ideal for Cisco-centric networks. Its DUAL algorithm provides instant failover using pre-computed Feasible Successors, making it highly reliable. The composite metric allows fine-grained path selection, and unequal-cost load balancing maximizes link utilization.

---

## References

- RFC 7868 — EIGRP
- Cisco Configuration Guide — EIGRP
- Odom, W. (2020). *CCNA 200-301 Official Cert Guide*. Cisco Press.
