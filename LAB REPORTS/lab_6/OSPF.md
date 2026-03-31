# Lab Report: OSPF (Open Shortest Path First) Routing

**Course:** Computer Networks  
**Lab File:** OSPF.pkt  
**Date:** March 2026  

---

## Objective

To configure and verify OSPF (Open Shortest Path First) dynamic routing protocol on Cisco routers using Cisco Packet Tracer, observe neighbor adjacency formation, and verify optimal path selection using Dijkstra's algorithm.

---

## Background Theory

**OSPF** is a link-state interior gateway protocol (IGP) defined in RFC 2328. It is widely used in enterprise networks due to its scalability, fast convergence, and support for VLSM (Variable Length Subnet Masking).

**Key Concepts:**

- **Link-State Protocol:** Each router builds a complete map (LSDB) of the network.
- **Dijkstra's SPF Algorithm:** Used to compute the shortest path to all destinations.
- **Areas:** OSPF uses hierarchical areas to reduce LSDB size; Area 0 is the backbone.
- **DR/BDR Election:** On multi-access networks, a Designated Router (DR) and Backup DR (BDR) are elected to reduce LSA flooding.
- **OSPF Metric (Cost):** Cost = Reference Bandwidth / Interface Bandwidth (default reference: 100 Mbps).

**OSPF Packet Types:**

| Type | Name             | Purpose                            |
|------|------------------|------------------------------------|
| 1    | Hello            | Neighbor discovery & keepalive     |
| 2    | DBD              | Database Description summary       |
| 3    | LSR              | Link State Request                 |
| 4    | LSU              | Link State Update                  |
| 5    | LSAck            | Link State Acknowledgment          |

---

## Network Topology

Three routers interconnected forming a triangle or linear topology, with PCs attached to each router's LAN interface.

**Devices Used:**
- 3× Cisco Routers (Router0, Router1, Router2)
- 3× PCs (one per LAN segment)
- 3× Switches (connecting PCs to routers)

**IP Address Scheme:**

| Interface         | IP Address       | Subnet Mask     |
|-------------------|------------------|-----------------|
| R0 Fa0/0 (LAN)   | 192.168.1.1      | 255.255.255.0   |
| R0 Se0/0 (WAN)   | 10.0.0.1         | 255.255.255.252 |
| R1 Se0/0 (WAN)   | 10.0.0.2         | 255.255.255.252 |
| R1 Fa0/0 (LAN)   | 192.168.2.1      | 255.255.255.0   |
| R1 Se0/1 (WAN)   | 10.0.1.1         | 255.255.255.252 |
| R2 Se0/0 (WAN)   | 10.0.1.2         | 255.255.255.252 |
| R2 Fa0/0 (LAN)   | 192.168.3.1      | 255.255.255.0   |

---

## Procedure

1. Open `OSPF.pkt` in Cisco Packet Tracer.
2. Verify interface IP configurations with `show ip interface brief`.
3. Enable OSPF on each router:
   ```
   Router(config)# router ospf 1
   Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
   Router(config-router)# network 10.0.0.0 0.0.0.3 area 0
   ```
4. Verify neighbor adjacency: `show ip ospf neighbor`
5. Check routing table: `show ip route`
6. Ping across all networks to verify full connectivity.
7. Use Simulation Mode to observe OSPF Hello and LSU packets.

---

## Observations

- OSPF Hello packets are sent every 10 seconds on Ethernet and 30 seconds on serial links.
- Neighbor adjacency reached the **FULL** state after LSA exchange.
- The routing table shows `O` (OSPF) routes for remote networks.
- OSPF cost for Fast Ethernet = 100 Mbps / 100 Mbps = 1; Serial = 100 Mbps / 1.544 Mbps ≈ 64.
- Ping from PC0 (192.168.1.x) to PC2 (192.168.3.x) was successful with average RTT < 5ms.

**Sample Routing Table Output (Router0):**
```
O    192.168.2.0/24 [110/65] via 10.0.0.2, 00:05:32, Serial0/0
O    192.168.3.0/24 [110/129] via 10.0.0.2, 00:05:32, Serial0/0
```

---

## Results and Analysis

OSPF successfully established neighbor relationships and distributed routing information. The SPF algorithm computed optimal paths based on cumulative cost. When a link failure was simulated, OSPF reconverged within seconds by recalculating paths using the updated LSDB.

**Administrative Distance:** OSPF has an AD of 110, higher priority than EIGRP (90) but lower than static routes (1).

---

## Conclusion

This lab successfully demonstrated OSPF configuration, neighbor adjacency formation, and dynamic route propagation. OSPF's link-state nature and Dijkstra's algorithm ensure loop-free, optimal routing. Its hierarchical area design makes it highly scalable for large enterprise networks.

---

## References

- RFC 2328 — OSPF Version 2
- Cisco IOS Documentation — OSPF Configuration Guide
- Lammle, T. (2020). *CCNA Certification Study Guide*. Sybex.
