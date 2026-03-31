# Lab Report: Lab 9 — Access Control Lists (ACLs)

**Course:** Computer Networks  
**Lab File:** lab_9.pkt  
**Date:** March 2026  

---

## Objective

To configure and verify Standard and Extended Access Control Lists (ACLs) on Cisco routers using Cisco Packet Tracer, to control network traffic and implement basic network security policies.

---

## Background Theory

**ACL (Access Control List):**
An ACL is an ordered set of rules (permit/deny statements) that a router uses to filter network traffic based on defined criteria. ACLs are applied to router interfaces to control inbound or outbound traffic.

**Types of ACLs:**

| Type              | Number Range | Match Criteria                    |
|-------------------|--------------|-----------------------------------|
| Standard IPv4     | 1–99         | Source IP address only            |
| Extended IPv4     | 100–199      | Source/Dest IP, Protocol, Port    |
| Named Standard    | N/A          | Source IP (named format)          |
| Named Extended    | N/A          | Full match criteria (named)       |

**ACL Processing Rules:**
1. Rules are evaluated top-to-bottom (first match wins).
2. An implicit `deny all` exists at the end of every ACL.
3. ACLs must be applied to an interface and direction (in/out) to take effect.

**Placement Guidelines:**
- **Standard ACLs:** Place as close to the destination as possible (match only source IP).
- **Extended ACLs:** Place as close to the source as possible (match detailed criteria).

---

## Network Topology

Multiple LANs connected through routers. ACLs are applied to restrict access between specific networks while allowing others.

**Devices Used:**
- 2× Cisco Routers
- 3× PCs (representing different departments)
- 2× Switches

**IP Address Scheme:**

| Device | IP Address     | Network         |
|--------|----------------|-----------------|
| PC0    | 192.168.1.10   | 192.168.1.0/24  |
| PC1    | 192.168.2.10   | 192.168.2.0/24  |
| PC2    | 192.168.3.10   | 192.168.3.0/24  |
| R0 G0  | 192.168.1.1    | Gateway for PC0 |
| R0 G1  | 10.0.0.1       | WAN link        |
| R1 G0  | 10.0.0.2       | WAN link        |
| R1 G1  | 192.168.2.1    | Gateway for PC1 |

**Security Policy:**
- PC0 (Finance) must NOT access PC2 (Guest Network).
- PC1 (IT) can access all networks.
- HTTP traffic from PC2 to Server is permitted; Telnet is denied.

---

## Procedure

**Standard ACL — Deny PC0 from accessing 192.168.3.0/24:**
```
Router(config)# access-list 10 deny 192.168.1.0 0.0.0.255
Router(config)# access-list 10 permit any
Router(config)# interface g0/1
Router(config-if)# ip access-group 10 out
```

**Extended ACL — Deny Telnet, permit HTTP from PC2:**
```
Router(config)# access-list 110 deny tcp 192.168.3.0 0.0.0.255 any eq 23
Router(config)# access-list 110 permit tcp 192.168.3.0 0.0.0.255 any eq 80
Router(config)# access-list 110 permit ip any any
Router(config)# interface g0/0
Router(config-if)# ip access-group 110 in
```

**Verification:**
```
show access-lists
show ip interface g0/1
```

---

## Observations

- After applying the standard ACL, PC0 could no longer ping PC2.
- PC1 maintained full connectivity to all networks.
- The extended ACL successfully blocked Telnet (port 23) from PC2 while allowing HTTP (port 80).
- `show access-lists` showed match counters incrementing with each test packet.
- The implicit deny was confirmed by blocked traffic not matching any permit rule.

**ACL Match Counters:**
```
access-list 10
  deny 192.168.1.0 0.0.0.255 (15 matches)
  permit any (42 matches)
```

---

## Results and Analysis

ACLs provided effective traffic filtering based on the defined security policy. Standard ACLs were adequate for simple source-based filtering, while extended ACLs offered granular control over protocol and port numbers. The first-match processing ensured predictable behavior.

**Connectivity Test Results:**

| Source → Destination          | Protocol | Result  |
|-------------------------------|----------|---------|
| PC0 → PC1                     | ICMP     | Permit  |
| PC0 → PC2                     | ICMP     | Deny    |
| PC2 → Server (HTTP)           | TCP 80   | Permit  |
| PC2 → Server (Telnet)         | TCP 23   | Deny    |

---

## Conclusion

This lab demonstrated the configuration and verification of both Standard and Extended ACLs. ACLs are a fundamental security tool in Cisco network environments. Proper placement of ACLs (close to source for extended, close to destination for standard) is critical for effective filtering without unintended impact on legitimate traffic. The implicit deny-all reinforces that explicit permit statements must be carefully planned.

---

## References

- Cisco IOS Security Configuration Guide — ACLs
- RFC 3704 — Ingress Filtering for Multi-homed Networks
- Odom, W. (2020). *CCNA 200-301 Official Cert Guide, Volume 2*. Cisco Press.
