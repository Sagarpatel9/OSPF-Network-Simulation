# OSPF Network Simulation Project

## Overview
This project simulates an enterprise-level network using **Cisco Packet Tracer**, implementing the **OSPF (Open Shortest Path First)** routing protocol. The setup demonstrates dynamic routing, IP addressing, and network connectivity using OSPF's Area 0.

## **Project Features**
1. **Dynamic Routing with OSPF:**
   - Configured OSPF with message-digest authentication for secure route advertisements.
   - Enabled OSPF for multiple subnets across four routers.
   - Used OSPF network types (point-to-point).

2. **DHCP Configuration:**
   - DHCP pool configured on the routers to assign IPs to connected devices dynamically.
   - DHCP lease time set for efficient IP address utilization.

3. **Router Security:**
   - Enabled **enable secret** for encrypted privileged access passwords.
   - Configured OSPF with **message-digest-key** to encrypt routing updates.
   - Encrypted all plain-text passwords using `service password-encryption`.

4. **Interface Management:**
   - Configured router interfaces with proper IP addressing and OSPF settings.
   - Disabled unused interfaces to improve security and performance.
  
## **Security Features**
1. **Encrypted Router Passwords:**
   - Used `enable secret` to configure the privileged mode password with encryption.
   - Enabled `service password-encryption` to encrypt all plain-text passwords stored in the configuration.

2. **OSPF Message-Digest Authentication:**
   - Configured OSPF with `message-digest-key` to ensure secure communication between routers.
   - Example configuration:
     ```plaintext
     interface FastEthernet1/1
      ip ospf authentication message-digest
      ip ospf message-digest-key 1 md5 [encrypted-password]
     ```

## **Project Goals**
- Understand OSPF configuration and database verification.
- Demonstrate routing across multiple subnets.
- Implement redundancy and efficient routing through OSPF.
- **Enhance network security** by implementing OSPF authentication using message-digest encryption.
- **Simplify IP management** through dynamic IP allocation using DHCP.
- Ensure scalability and reliability by configuring the network for future IPv6 support.


## Topology
![Network Topology](diagram/OSPF_typology.png)

### Network Details
- **Routers**: R1, R2, R3, R4
- **OSPF Area**: Area 0
- **Dynamic IP Allocation**: DHCP enabled on all subnets
- **Interfaces**: Configured with IP addressing for network communication

## File Descriptions
### **configs/**
Contains the startup configurations for the routers:
- `R1_i1_startup-config.cfg`: Startup configuration for Router 1.
- `R2_i2_startup-config.cfg`: Startup configuration for Router 2.
- `R3_i3_startup-config.cfg`: Startup configuration for Router 3.
- `R4_i4_startup-config.cfg`: Startup configuration for Router 4.

### **diagrams/**
Includes visual aids:
- `network_topology.png`: Visual representation of the network.
- `routerXospfdatabase.png`: Screenshots of OSPF databases for verification.


## Configuration Summary
- OSPF routing enabled with router IDs `1.1.1.1`, `2.2.2.2`, `3.3.3.3`, `4.4.4.4`.
- Each router connected to at least one subnet with DHCP for host IP assignment.
- Configurations verified using commands like `show ip ospf database`.





