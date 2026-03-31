# Lab Report: Lab 8 — NAT and PAT (Network Address Translation)

**Course:** Computer Networks  
**Lab File:** lab8.pkt  
**Date:** March 2026  

---

## Objective

To configure and verify Static NAT, Dynamic NAT, and PAT (Port Address Translation / NAT Overload) on Cisco routers using Cisco Packet Tracer, enabling private network hosts to access the internet using public IP addresses.

---

## Background Theory

**NAT (Network Address Translation):**
NAT modifies IP address information in packet headers as they pass through a router, allowing private (RFC 1918) addresses to be translated to routable public addresses.

**Types of NAT:**

| Type          | Description                                       | Use Case                     |
|---------------|---------------------------------------------------|------------------------------|
| Static NAT    | One-to-one permanent mapping                      | Servers needing fixed public IP |
| Dynamic NAT   | One-to-one mapping from a pool (temporary)        | Multiple clients, public pool |
| PAT / Overload| Many-to-one using port numbers                    | Most home/enterprise internet |

**Private Address Ranges (RFC 1918):**
- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.0.0/16

**NAT Terminology:**
- **Inside Local:** Private IP of internal host
- **Inside Global:** Public IP representing the host externally
- **Outside Local:** Destination as seen from inside
- **Outside Global:** Actual external destination IP

**PAT Operation:**
PAT translates multiple private IPs to a single public IP by using unique source port numbers to distinguish sessions. This allows thousands of simultaneous connections through one public address.

---

## Network Topology

An inside private network connected to an outside (simulated Internet) via a NAT-enabled router.

**Devices Used:**
- 1× Cisco Router (NAT gateway)
- 3× Internal PCs (private addresses)
- 1× External Server (simulated internet)

**IP Address Scheme:**

| Device        | IP Address       | Type          |
|---------------|------------------|---------------|
| PC0           | 192.168.1.10     | Inside Local  |
| PC1           | 192.168.1.11     | Inside Local  |
| PC2           | 192.168.1.12     | Inside Local  |
| Router Inside | 192.168.1.1      | Inside        |
| Router Outside| 203.0.113.1      | Outside       |
| Web Server    | 8.8.8.8          | Outside Global|

---

## Procedure

**Define Inside/Outside Interfaces:**
```
Router(config)# interface fa0/0
Router(config-if)# ip nat inside

Router(config)# interface fa0/1
Router(config-if)# ip nat outside
```

**Static NAT (for PC0 server):**
```
Router(config)# ip nat inside source static 192.168.1.10 203.0.113.10
```

**Dynamic NAT (for PC1 and PC2):**
```
Router(config)# ip nat pool PUBLIC_POOL 203.0.113.20 203.0.113.25 netmask 255.255.255.248
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# ip nat inside source list 1 pool PUBLIC_POOL
```

**PAT / NAT Overload:**
```
Router(config)# ip nat inside source list 1 interface fa0/1 overload
```

**Verification:**
```
show ip nat translations
show ip nat statistics
```

---

## Observations

- Static NAT created a permanent entry in the NAT table for PC0.
- Dynamic NAT assigned temporary public IPs from the pool when PCs sent traffic.
- PAT allowed all three PCs to share one public IP (203.0.113.1) using different port numbers.
- `show ip nat translations` revealed active session mappings with protocol and port details.
- NAT translations were removed after TCP sessions closed or timeout expired.

**NAT Translation Table Sample:**
```
Pro  Inside global       Inside local        Outside local      Outside global
tcp  203.0.113.1:1025   192.168.1.10:1025   8.8.8.8:80        8.8.8.8:80
tcp  203.0.113.1:1026   192.168.1.11:3201   8.8.8.8:80        8.8.8.8:80
tcp  203.0.113.1:1027   192.168.1.12:4502   8.8.8.8:80        8.8.8.8:80
```

---

## Results and Analysis

All three NAT types functioned correctly. PAT (overload) proved the most practical, allowing all hosts to share a single public IP. The NAT table confirmed correct address translation and port assignment. The router maintained state for each active session.

**NAT Type Comparison:**

| Feature             | Static NAT    | Dynamic NAT   | PAT           |
|---------------------|---------------|---------------|---------------|
| Mapping Type        | Permanent 1:1 | Dynamic 1:1   | Many:1        |
| Public IPs Required | 1 per host    | Pool required | 1 total       |
| Session Tracking    | No            | No            | Yes (ports)   |
| Best For            | Servers       | Mid-size corp | Home/ISP      |

---

## Conclusion

This lab successfully demonstrated all three forms of NAT. PAT (NAT Overload) emerged as the most efficient solution, enabling many private hosts to share a single public IP address through port multiplexing. This is the technology that enables billions of devices behind home routers to access the internet using a single ISP-assigned address. NAT also provides a layer of security by hiding internal IP structure from outside networks.

---

## References

- RFC 3022 — Traditional IP Network Address Translator
- Cisco NAT Configuration Guide
- Lammle, T. (2020). *CCNA Certification Study Guide*. Sybex.
