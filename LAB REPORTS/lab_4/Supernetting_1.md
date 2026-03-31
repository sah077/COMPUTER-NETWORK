# Lab Report: Supernetting (Route Aggregation / CIDR)

**Course:** Computer Networks  
**Lab File:** Supernetting_1.pkt  
**Date:** March 2026  

---

## Objective

To understand and apply supernetting (Classless Inter-Domain Routing — CIDR) by aggregating multiple contiguous network addresses into a single summarized route, reducing routing table size in Cisco Packet Tracer.

---

## Background Theory

**Supernetting** (also called route aggregation or route summarization) is the process of combining multiple contiguous IP networks into a single, larger network block. It is the opposite of subnetting.

**CIDR (Classless Inter-Domain Routing):**
- Introduced to overcome the exhaustion of IPv4 classes.
- Uses prefix notation (e.g., `/22`) instead of classful masks.
- Allows flexible block sizes and efficient address allocation.

**How to Supernet:**
Given networks:
- 192.168.0.0/24
- 192.168.1.0/24
- 192.168.2.0/24
- 192.168.3.0/24

Convert to binary, find common prefix bits:
```
192.168.00000000.0  → 192.168.0.0
192.168.00000001.0  → 192.168.1.0
192.168.00000010.0  → 192.168.2.0
192.168.00000011.0  → 192.168.3.0
                      ^^^^^^^^^^ last 2 bits differ
```
Common prefix: 22 bits → Supernet = **192.168.0.0/22**

**Benefits:**
- Reduces routing table entries (scalability)
- Decreases router processing overhead
- Faster convergence in dynamic routing
- Limits LSA/Update flooding scope

---

## Network Topology

Multiple routers each advertising subnets that can be summarized into a supernet. The aggregating router announces a single summarized prefix to upstream routers.

**Devices Used:**
- 3× Cisco Routers
- 4× Networks (to be summarized)

**IP Address Scheme:**

| Network          | Prefix | Purpose         |
|------------------|--------|-----------------|
| 192.168.0.0/24   | /24    | LAN Segment 1   |
| 192.168.1.0/24   | /24    | LAN Segment 2   |
| 192.168.2.0/24   | /24    | LAN Segment 3   |
| 192.168.3.0/24   | /24    | LAN Segment 4   |
| **192.168.0.0/22** | **/22** | **Supernet**  |

---

## Procedure

1. Open `Supernetting_1.pkt` in Cisco Packet Tracer.
2. Verify all subnets are reachable individually on the aggregating router.
3. Calculate the supernet manually:
   - Find the smallest common prefix across all networks.
   - Verify no gaps exist between aggregated blocks.
4. Apply route summarization on the interface facing upstream:
   ```
   Router(config-router)# summary-address eigrp 100 192.168.0.0 255.255.252.0
   ```
   Or for OSPF with area range:
   ```
   Router(config-router)# area 0 range 192.168.0.0 255.255.252.0
   ```
5. Verify the upstream router sees only the summarized route.
6. Test connectivity to all four subnets from a remote host.

---

## Observations

- Before summarization: upstream router had 4 separate `/24` routes.
- After summarization: upstream router shows only `192.168.0.0/22`.
- All four subnets remained reachable through the aggregated prefix.
- Routing table size reduced by 75% (4 entries → 1 entry) for those networks.
- The summarizing router installed a Null0 route for `192.168.0.0/22` to prevent routing loops.

---

## Results and Analysis

**Routing Table Before Summarization:**
```
O  192.168.0.0/24 [110/65] via ...
O  192.168.1.0/24 [110/65] via ...
O  192.168.2.0/24 [110/65] via ...
O  192.168.3.0/24 [110/65] via ...
```

**Routing Table After Summarization:**
```
O  192.168.0.0/22 [110/65] via ...
```

**Null0 Route (Loop Prevention):**
```
O  192.168.0.0/22 is a summary, 00:10:00, Null0
```

---

## Conclusion

Supernetting via CIDR and route summarization is essential for scalable internet and enterprise routing. By aggregating four /24 networks into a single /22 prefix, this lab demonstrated a 75% reduction in routing entries. This technique improves scalability, reduces convergence time, and limits the propagation of routing updates across the network. Proper design of address blocks (contiguous, power-of-2 aligned) is essential for effective summarization.

---

## References

- RFC 1519 — CIDR (Classless Inter-Domain Routing)
- Cisco Route Summarization Configuration Guide
- Forouzan, B. A. (2012). *Data Communications and Networking*. McGraw-Hill.
