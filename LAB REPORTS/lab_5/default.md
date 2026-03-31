# Lab Report: Default Routing Configuration

**Course:** Computer Networks  
**Lab File:** default.pkt  
**Date:** March 2026  

---

## Objective

To configure and verify default routes (gateway of last resort) on Cisco routers using Cisco Packet Tracer, and understand when and why default routing is used in real-world network scenarios.

---

## Background Theory

A **default route** (also called gateway of last resort) is a routing table entry used when no specific route matches the destination IP address. It is the "catch-all" route that forwards packets to a next-hop when no more specific path is known.

**Default Route Characteristics:**
- Network: `0.0.0.0`
- Subnet Mask: `0.0.0.0`
- Notation: `0.0.0.0/0`
- Lowest specificity — matches any destination

**Use Cases:**
- Stub networks connected to the internet via a single ISP
- Branch office routers pointing all unknown traffic to headquarters
- Simplifying routing tables in spoke sites of hub-and-spoke topologies

**Configuration Command:**
```
Router(config)# ip route 0.0.0.0 0.0.0.0 [next-hop-ip | exit-interface]
```

**Propagating with OSPF:**
```
Router(config-router)# default-information originate
```

---

## Network Topology

A simple topology with a stub router (simulating a branch/edge router) connected to a core/ISP router. The stub router uses only a default route to reach all external networks.

**Devices Used:**
- 2× Cisco Routers (Router0 — Edge, Router1 — Core/ISP)
- 2× PCs (one per LAN)
- 2× Switches

**IP Address Scheme:**

| Interface         | IP Address      | Subnet Mask     |
|-------------------|-----------------|-----------------|
| Edge Fa0/0 (LAN)  | 192.168.1.1     | 255.255.255.0   |
| Edge Se0/0        | 203.0.113.1     | 255.255.255.252 |
| Core Se0/0        | 203.0.113.2     | 255.255.255.252 |
| Core Fa0/0        | 10.0.0.1        | 255.255.255.0   |

---

## Procedure

1. Open `default.pkt` in Cisco Packet Tracer.
2. Verify interface configurations with `show ip interface brief`.
3. Configure the default route on the Edge router:
   ```
   Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.2
   ```
4. Optionally configure specific return routes on Core router or use OSPF with `default-information originate`.
5. Verify routing table: `show ip route`
6. Look for `S* 0.0.0.0/0` — the asterisk indicates it is the gateway of last resort.
7. Test connectivity: ping from PC0 (LAN) to PC1 (remote network).
8. Observe that all traffic from the edge router uses the single default route.

---

## Observations

- The routing table on the Edge router showed only:
  - `C 192.168.1.0/24` — directly connected LAN
  - `S* 0.0.0.0/0 via 203.0.113.2` — default route
- Pings from PC0 to remote addresses succeeded through the default route.
- Without a specific return route on the Core router, ICMP replies failed — highlighting the importance of bidirectional routing.
- Once a return static route was added on Core, full connectivity was established.

---

## Results and Analysis

The default route successfully forwarded all packets with unknown destinations to the core router. This dramatically simplified the routing table of the edge router.

**Routing Table Comparison:**

| Router      | Route Entries | Method              |
|-------------|---------------|---------------------|
| Edge Router | 2             | Default + Connected |
| Core Router | 4+            | Static / Dynamic    |

**Gateway of Last Resort:**  
The `show ip route` output confirmed the Gateway of Last Resort was set to `203.0.113.2`.

---

## Conclusion

Default routing is a powerful and simple technique for stub network scenarios. By configuring a single `0.0.0.0/0` route, edge routers can forward all unknown traffic toward the network core without needing knowledge of every destination. This reduces routing table size, simplifies configuration, and is ideal for single-exit-point network designs such as branch offices or home internet gateways.

---

## References

- Cisco IOS IP Routing Configuration Guide
- RFC 1122 — Requirements for Internet Hosts
- Lammle, T. (2020). *CCNA Certification Study Guide*. Sybex.
