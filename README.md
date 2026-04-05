# Network Infrastructure & DHCP Configuration Lab

## Overview
A hands-on networking lab built in **Cisco Packet Tracer** simulating a multi-site enterprise network with four routers, four subnets, and dynamic IP assignment via DHCP. This project demonstrates the networking fundamentals that IT helpdesk and desktop support technicians use daily — IP addressing, DHCP troubleshooting, routing verification, and cross-subnet connectivity testing.

---

## Why This Matters for Helpdesk

Helpdesk technicians are the first point of contact when users report "I can't connect to the network" or "My computer isn't getting an IP address." This lab covers the skills needed to diagnose and resolve those issues:

- Understanding how DHCP assigns IPs to client machines
- Knowing how to verify connectivity with `ping` across subnets
- Reading routing tables to understand why traffic can or can't reach its destination
- Identifying whether an issue is local (wrong IP, expired lease) or network-wide (routing problem)

---

## Lab Environment

| Component       | Details                                      |
|-----------------|----------------------------------------------|
| **Tool**        | Cisco Packet Tracer                          |
| **Routers**     | R1, R2, R3, R4 (Cisco IOS 15.2)             |
| **Switches**    | Switch1, Switch2, Switch3, Switch4           |
| **End Devices** | PC1, PC2, PC3, PC4 (DHCP clients)           |
| **Routing**     | OSPF Area 0 (dynamic routing)               |
| **IP Assignment** | DHCP pools on each router                 |

---

## Network Topology

![Network Topology](diagrams/OSPF_typology.png)

---

## Subnet & DHCP Summary

| Router | DHCP Pool | Subnet             | Default Gateway | Lease Time |
|--------|-----------|---------------------|-----------------|------------|
| R1     | R1POOL    | 192.168.1.0/24      | 192.168.1.1     | 4 days     |
| R2     | R2POOL    | 192.168.4.0/24      | 192.168.4.1     | 4 days     |
| R3     | R3POOL    | 192.168.2.0/24      | 192.168.2.1     | 6 days     |
| R4     | R4POOL    | 192.168.5.0/24      | 192.168.5.1     | 4 days     |

Each router acts as both the default gateway and the DHCP server for its local subnet. Client PCs (PC1–PC4) receive their IP addresses automatically via DHCP.

---

## Router-to-Router Links

| Link         | Subnet          | Connects       |
|--------------|-----------------|----------------|
| 101.1.1.0/24 | R1 ↔ R3         | Fa0/0 on both  |
| 102.1.1.0/24 | R1 ↔ R2         | Fa1/0 on both  |
| 103.1.1.0/24 | R2 ↔ R4         | Fa0/0 ↔ Fa1/0  |
| 104.1.1.0/24 | R3 ↔ R4         | Fa1/0 ↔ Fa0/0  |

---

## What Was Configured

### 1. DHCP Pools
Each router was configured with a DHCP pool to dynamically assign IP addresses to connected PCs. This eliminates manual IP configuration on every device — the same way corporate networks handle hundreds of workstations automatically.

**Example — R1 DHCP Configuration:**
```
ip dhcp pool R1POOL
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 lease 4
```

### 2. OSPF Dynamic Routing
OSPF (Open Shortest Path First) was configured across all four routers so that every subnet can communicate with every other subnet automatically. When a helpdesk user says "I can ping my local server but can't reach the finance department's printer," understanding routing is what helps you diagnose whether it's a routing issue or something else.

**Each router advertises its connected subnets to OSPF Area 0:**
- R1 (Router ID 1.1.1.1): advertises 192.168.1.0, 101.1.1.0, 102.1.1.0
- R2 (Router ID 2.2.2.2): advertises 192.168.4.0, 102.1.1.0, 103.1.1.0
- R3 (Router ID 3.3.3.3): advertises 192.168.2.0, 101.1.1.0, 104.1.1.0
- R4 (Router ID 4.4.4.4): advertises 192.168.5.0, 103.1.1.0, 104.1.1.0

### 3. Interface Configuration
Each router interface was configured with a static IP on the WAN side (router-to-router links) and acts as the gateway on the LAN side (user subnet).

![Router Interface Configuration](diagrams/ospf_config.png)

---

## Verification & Troubleshooting

### OSPF Database Verification
The `show ip ospf database` command confirms that all routers have learned about each other and are sharing routing information correctly. If a router is missing from this list, it means there's a connectivity or configuration issue — exactly the kind of thing that causes "I can't reach the server" tickets.

**R1 OSPF Database:**

![R1 OSPF Database](diagrams/router1ospfdatabase.png)

**R2 OSPF Database:**

![R2 OSPF Database](diagrams/router2ospfdatabase.png)

**R3 OSPF Database:**

![R3 OSPF Database](diagrams/router3ospfdatabase.png)

**R4 OSPF Database:**

![R4 OSPF Database](diagrams/router4ospfdatabase.png)

All four routers (1.1.1.1, 2.2.2.2, 3.3.3.3, 4.4.4.4) appear in every database — confirming full network convergence.

### Common Helpdesk Troubleshooting Commands Used
| Command                  | What It Does                                          | When to Use It                              |
|--------------------------|-------------------------------------------------------|---------------------------------------------|
| `ipconfig /all`          | Shows IP, subnet mask, gateway, DNS, DHCP server      | User reports "no internet" or "can't connect"|
| `ipconfig /release`      | Releases current DHCP lease                           | Stale or conflicting IP address              |
| `ipconfig /renew`        | Requests a new IP from the DHCP server                | After release, or when IP shows 169.254.x.x  |
| `ping <gateway>`         | Tests connectivity to the default gateway             | Checks if the local network is reachable     |
| `ping <remote subnet>`   | Tests connectivity to another subnet                  | "I can't reach the finance printer"          |
| `tracert <destination>`  | Shows the path packets take across routers            | Identifies where connectivity breaks         |
| `show ip ospf database`  | Lists all routers known to OSPF                       | Verifies routing is working (router side)    |
| `show ip dhcp binding`   | Shows which IPs have been assigned by DHCP            | Checks if a device received an IP            |
| `show ip route`          | Displays the router's routing table                   | Confirms routes exist to destination subnets |

---

## Helpdesk Scenarios This Lab Simulates

| Scenario | Symptoms | Diagnosis | Resolution |
|----------|----------|-----------|------------|
| PC not getting an IP | `ipconfig` shows 169.254.x.x (APIPA) | DHCP pool exhausted or cable disconnected | Check cable → `ipconfig /release` → `ipconfig /renew` → verify DHCP pool on router |
| Can ping gateway but not remote subnet | Ping to 192.168.1.1 works, ping to 192.168.5.1 fails | Routing issue — OSPF may not have converged | Run `show ip ospf database` on routers to check if all routers are visible |
| Slow or intermittent connectivity | Pages load slowly, pings show high latency | Possible duplex mismatch or congested link | Check interface stats with `show interface` for errors/collisions |
| IP address conflict | Two devices showing same IP | DHCP lease conflict or static IP overlapping DHCP range | Identify conflicting device → release/renew → exclude static IPs from DHCP pool |
| User can't reach a specific department | "I can ping everything except the finance subnet" | Missing route or interface down on path | `tracert` to find where it stops → check that router's interfaces and OSPF config |

---

## Configuration Files

Located in the `configs/` directory:
- `R1_i1_startup-config.cfg` — Router 1 (gateway for 192.168.1.0/24)
- `R2_i2_startup-config.cfg` — Router 2 (gateway for 192.168.4.0/24)
- `R3_i3_startup-config.cfg` — Router 3 (gateway for 192.168.2.0/24)
- `R4_i4_startup-config.cfg` — Router 4 (gateway for 192.168.5.0/24)

---

## Tools & Technologies

| Tool / Technology   | Purpose                                    |
|---------------------|--------------------------------------------|
| Cisco Packet Tracer | Network simulation and lab environment     |
| OSPF                | Dynamic routing protocol for multi-subnet connectivity |
| DHCP                | Automatic IP address assignment to client PCs |
| Cisco IOS CLI       | Router configuration and troubleshooting   |
| ipconfig / ping / tracert | Endpoint troubleshooting commands    |

---

## Key Takeaways
- DHCP automates IP assignment across an enterprise — helpdesk techs need to understand lease times, pools, and what happens when DHCP fails (169.254.x.x APIPA addresses)
- Dynamic routing (OSPF) ensures all subnets can talk to each other without manual configuration on every router
- Troubleshooting tools like `ipconfig`, `ping`, and `tracert` are the first things a helpdesk technician runs when a user reports connectivity issues
- Reading OSPF databases and routing tables helps escalate network issues to Tier 2/3 with useful diagnostic information instead of just saying "it doesn't work"
