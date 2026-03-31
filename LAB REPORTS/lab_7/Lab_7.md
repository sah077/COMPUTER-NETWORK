# VLAN Configuration and Inter-VLAN Routing Lab Report

A comprehensive lab report documenting the configuration of VLANs, switch port assignments, and Inter-VLAN routing using router-on-a-stick method in Cisco Packet Tracer.

## Overview

This lab demonstrates network segmentation using VLANs and enables communication between different VLANs through Inter-VLAN routing. The implementation uses a single router interface with subinterfaces (router-on-a-stick) to route traffic between VLANs.

## Lab Objectives

- Configure VLANs on a Cisco switch
- Assign switch ports to specific VLANs
- Configure switchport modes (access and trunk)
- Implement Inter-VLAN routing using router subinterfaces
- Configure 802.1Q encapsulation on router subinterfaces
- Test connectivity within and between VLANs

## Network Topology

```
                    Router0
                    (1941)
                      |
                   Gi0/0 (Trunk)
                      |
                   Switch0
                    (2960)
                 /    |    \    \
              Fa0/1 Fa0/2 Fa0/3 Fa0/4
               /      |      \      \
             PC0    PC1     PC2    PC3
          VLAN 10 VLAN 10 VLAN 20 VLAN 20
```

## Network Configuration

### VLANs

| VLAN ID | VLAN Name   | Network          | Gateway       | Purpose    |
|---------|-------------|------------------|---------------|------------|
| 10      | Computer    | 192.168.10.0/24  | 192.168.10.1  | Computer department |
| 20      | Electronics | 192.168.20.0/24  | 192.168.20.1  | Electronics department |

### Device IP Addressing

| Device  | Interface/Port  | IP Address      | Subnet Mask     | VLAN | Gateway       |
|---------|-----------------|-----------------|-----------------|------|---------------|
| Router0 | Gi0/0.10        | 192.168.10.1    | 255.255.255.0   | 10   | —             |
| Router0 | Gi0/0.20        | 192.168.20.1    | 255.255.255.0   | 20   | —             |
| PC0     | Fa0/1 (Switch)  | 192.168.10.2    | 255.255.255.0   | 10   | 192.168.10.1  |
| PC1     | Fa0/2 (Switch)  | 192.168.10.3    | 255.255.255.0   | 10   | 192.168.10.1  |
| PC2     | Fa0/3 (Switch)  | 192.168.20.2    | 255.255.255.0   | 20   | 192.168.20.1  |
| PC3     | Fa0/4 (Switch)  | 192.168.20.3    | 255.255.255.0   | 20   | 192.168.20.1  |

### Port Assignments

| Switch Port | VLAN | Mode   | Connected Device |
|-------------|------|--------|------------------|
| Fa0/1       | 10   | Access | PC0              |
| Fa0/2       | 10   | Access | PC1              |
| Fa0/3       | 20   | Access | PC2              |
| Fa0/4       | 20   | Access | PC3              |
| Fa0/5       | All  | Trunk  | Router0 Gi0/0    |

## Configuration Commands

### Switch Configuration

#### Create VLANs
```
Switch(config)# vlan 10
Switch(config-vlan)# name Computer
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name Electronics
Switch(config-vlan)# exit
```

#### Assign Ports to VLAN 10
```
Switch(config)# interface range fa0/1-2
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# exit
```

#### Assign Ports to VLAN 20
```
Switch(config)# interface range fa0/3-4
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
Switch(config-if-range)# exit
```

#### Configure Trunk Port
```
Switch(config)# interface fa0/5
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
```

### Router Configuration (Inter-VLAN Routing)

#### Configure Subinterface for VLAN 10
```
Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit
```

#### Configure Subinterface for VLAN 20
```
Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit
```

#### Enable Physical Interface
```
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# exit
```

## Verification Commands

### Switch Verification
```
Switch# show vlan brief
```
Displays all VLANs and their assigned ports.

```
Switch# show interfaces trunk
```
Displays trunk port configuration and allowed VLANs.

### Router Verification
```
Router# show ip interface brief
```
Displays all interfaces, their IP addresses, and status.

```
Router# show ip route
```
Displays routing table with directly connected networks.

## Connectivity Testing

### Intra-VLAN Communication
Test connectivity between devices in the same VLAN (should work):
```
PC0> ping 192.168.10.3    # PC0 to PC1 (both VLAN 10)
PC2> ping 192.168.20.3    # PC2 to PC3 (both VLAN 20)
```

### Inter-VLAN Communication
Test connectivity between devices in different VLANs (requires routing):
```
PC0> ping 192.168.20.2    # PC0 (VLAN 10) to PC2 (VLAN 20)
PC1> ping 192.168.20.3    # PC1 (VLAN 10) to PC3 (VLAN 20)
```

### Gateway Testing
Test connectivity to default gateway:
```
PC0> ping 192.168.10.1    # VLAN 10 gateway
PC2> ping 192.168.20.1    # VLAN 20 gateway
```

## Key Concepts

### VLANs (Virtual Local Area Networks)
- Logical segmentation of a network at Layer 2
- Improves security by isolating broadcast domains
- Reduces broadcast traffic
- Enables better network management and organization

### Switchport Modes
- **Access Mode**: Belongs to a single VLAN, used for end devices
- **Trunk Mode**: Carries traffic for multiple VLANs, used between switches and routers

### Inter-VLAN Routing Methods
- **Router-on-a-Stick**: Single physical interface with multiple subinterfaces (used in this lab)
- **Layer 3 Switch**: Switch with routing capabilities
- **Separate Physical Interfaces**: One interface per VLAN (not scalable)

### 802.1Q Encapsulation
- Industry standard VLAN tagging protocol
- Adds 4-byte VLAN tag to Ethernet frames
- Enables multiple VLANs over a single trunk link

## Lab Results

✅ VLANs successfully created with descriptive names  
✅ Switch ports correctly assigned to VLANs  
✅ Trunk link established between switch and router  
✅ Router subinterfaces configured with 802.1Q encapsulation  
✅ Intra-VLAN communication works without routing  
✅ Inter-VLAN communication successful through router  
✅ All connectivity tests passed  

## Learning Outcomes

Upon completion of this lab, you will understand:

- How to create and manage VLANs on Cisco switches
- Difference between access and trunk switchport modes
- Router-on-a-stick Inter-VLAN routing configuration
- 802.1Q VLAN tagging and encapsulation
- Network segmentation benefits and use cases
- Troubleshooting VLAN connectivity issues
- Best practices for VLAN design and implementation

## Troubleshooting Tips

### Issue: No connectivity between VLANs
- Verify router subinterfaces are configured correctly
- Check 802.1Q encapsulation matches VLAN IDs
- Ensure trunk port is configured on switch
- Verify router physical interface is up (`no shutdown`)

### Issue: PCs can't reach gateway
- Check PC default gateway configuration
- Verify VLAN assignments on switch ports
- Ensure router subinterface has correct IP address

### Issue: Trunk not working
- Verify both ends configured as trunk
- Check allowed VLANs on trunk interface
- Ensure native VLAN matches (if configured)

## File Contents

- `VLAN_Configuration_Lab_Report.docx` - Complete lab report
- `lab_7.pkt` - Cisco Packet Tracer simulation file
- Network topology diagrams (embedded in report)
- Configuration notes (embedded in report)

## Technologies Used

- **Cisco Packet Tracer** - Network simulation tool
- **Cisco 2960 Switch** - Layer 2 switch with VLAN support
- **Cisco 1941 Router** - Router with subinterface capability
- **VLANs** - Virtual network segmentation
- **802.1Q** - VLAN tagging protocol
- **Inter-VLAN Routing** - Layer 3 routing between VLANs

## Prerequisites

- Basic understanding of networking concepts (OSI model, IP addressing)
- Familiarity with Cisco IOS command-line interface
- Knowledge of subnetting and IP address schemes
- Cisco Packet Tracer installed (for simulation file)

## Use Cases

This VLAN configuration is applicable to:

- **Departmental Segmentation**: Separate departments (HR, IT, Sales)
- **Security Isolation**: Isolate sensitive systems
- **Guest Networks**: Separate guest traffic from corporate network
- **IoT Device Management**: Isolate IoT devices
- **Voice and Data Separation**: QoS for VoIP traffic

## Best Practices

- Use descriptive VLAN names for easy identification
- Document VLAN assignments and IP schemes
- Limit VLAN scope to Layer 2 domains
- Use trunk ports only where necessary
- Implement proper security on unused ports
- Use consistent VLAN numbering across network
- Reserve VLAN 1 for management (don't use for data)

## Advanced Topics

For further learning, explore:

- **VTP (VLAN Trunking Protocol)**: Centralized VLAN management
- **Native VLANs**: Untagged VLAN on trunk links
- **Voice VLANs**: Dedicated VLANs for VoIP phones
- **Private VLANs**: Additional isolation within VLANs
- **Layer 3 Switching**: SVI-based Inter-VLAN routing
- **Dynamic VLAN Assignment**: RADIUS-based VLAN assignment

## References

- Cisco IOS Configuration Guides
- Cisco Packet Tracer Documentation
- IEEE 802.1Q Standard
- CCNA Routing and Switching Study Materials

---

**Lab Number**: Lab 7  
**Created**: February 2026  
**Tool**: Cisco Packet Tracer  
