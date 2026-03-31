# Lab Report: Hub-Based Network Communication

**Course:** Computer Networks  
**Lab File:** hub.pkt  
**Date:** March 2026  

---

## Objective

To understand the behavior of a hub in a Local Area Network (LAN), observe how hubs broadcast frames to all connected devices, and analyze the impact on network performance and security using Cisco Packet Tracer.

---

## Background Theory

A **hub** is a basic Layer 1 (Physical Layer) networking device that connects multiple Ethernet devices in a network. Unlike switches, hubs operate without any intelligence — they simply repeat electrical signals received on one port to all other ports.

**Key Characteristics of Hubs:**
- Operate at OSI Layer 1 (Physical Layer)
- Create a single collision domain for all connected devices
- Broadcast all incoming frames to every port
- Half-duplex communication only
- No MAC address learning capability

**Collision Domain:**  
All devices connected to a hub share the same collision domain. When two devices transmit simultaneously, a collision occurs, and all devices must use CSMA/CD (Carrier Sense Multiple Access with Collision Detection) to manage retransmission.

---

## Network Topology

The simulation uses a star topology with a central hub connecting multiple end devices (PCs). All PCs are assigned IP addresses in the same subnet, allowing them to communicate through the hub.

**Devices Used:**
- 1× Hub (generic hub)
- 4× PCs (PC0, PC1, PC2, PC3)

**IP Address Scheme:**

| Device | IP Address     | Subnet Mask   |
|--------|----------------|---------------|
| PC0    | 192.168.1.1    | 255.255.255.0 |
| PC1    | 192.168.1.2    | 255.255.255.0 |
| PC2    | 192.168.1.3    | 255.255.255.0 |
| PC3    | 192.168.1.4    | 255.255.255.0 |

---

## Procedure

1. Open the `hub.pkt` file in Cisco Packet Tracer.
2. Observe the topology — a hub connecting multiple PCs.
3. Use **Simulation Mode** to observe frame propagation.
4. Send a ping from PC0 to PC1.
5. Observe that the frame is broadcast to PC2 and PC3 as well.
6. Record the behavior and compare to switch behavior.

---

## Observations

- When PC0 sends a frame to PC1, the hub floods the frame to all ports (PC1, PC2, PC3).
- PC2 and PC3 receive the frame but discard it after checking the destination MAC address.
- ARP requests are broadcast and received by all hosts.
- In Simulation Mode, colored PDU envelopes visually confirm the broadcast behavior.
- With multiple simultaneous transmissions, collisions were detected.

---

## Results and Analysis

**Broadcast Behavior:** All frames sent through the hub are forwarded to every connected device, regardless of destination. This creates unnecessary traffic and reduces available bandwidth for each device.

**Security Concern:** Any device connected to the hub can capture traffic meant for other devices using a packet sniffer — a significant security vulnerability.

**Performance:** With 4 devices sharing the same collision domain, the effective throughput per device decreases as traffic increases. The theoretical bandwidth (e.g., 10 Mbps) is shared among all devices.

**Comparison with Switch:**

| Feature            | Hub              | Switch            |
|--------------------|------------------|-------------------|
| OSI Layer          | Layer 1          | Layer 2           |
| Collision Domains  | 1 (shared)       | Per port          |
| Frame Forwarding   | Broadcast all    | Selective         |
| MAC Learning       | No               | Yes               |
| Security           | Low              | Higher            |

---

## Conclusion

This lab demonstrated that hubs are simple, low-cost devices that operate by broadcasting all incoming data to every port. While functional for very small networks, hubs are inefficient and insecure compared to switches. The single collision domain causes performance degradation with increased traffic. Modern networks have largely replaced hubs with switches to improve performance, reduce collisions, and enhance security.

---

## References

- Cisco Networking Academy — CCNA Curriculum
- Tanenbaum, A. S. (2011). *Computer Networks* (5th ed.). Pearson.
- Forouzan, B. A. (2012). *Data Communications and Networking* (5th ed.). McGraw-Hill.
