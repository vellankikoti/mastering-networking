# Lab 20: Configuring MPLS (Multiprotocol Label Switching)

## **Objective**
- Understand MPLS concepts and its role in modern networking.
- Configure MPLS on core routers for traffic engineering.
- Verify MPLS functionality and test its performance.

---

## **Scenario**
Your organization requires MPLS to optimize routing between the following environments:

| **Environment** | **IPv4 Network Range**       |
|------------------|-----------------------------|
| Dev             | 192.168.10.0/24            |
| QA              | 192.168.20.0/24            |
| Stage           | 192.168.30.0/24            |
| Production       | 192.168.40.0/24            |

You will configure MPLS on the core network, create label-switched paths (LSPs), and verify MPLS functionality.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**

1. Use Cisco Packet Tracer or GNS3.
2. Add three routers:
   - **Router A:** Edge router for Dev and QA.
   - **Router B:** Core router connecting all edge routers.
   - **Router C:** Edge router for Stage and Production.

---

### **Step 2: Enable MPLS on Core Routers**

#### **1. Configure MPLS on Router B**
1. Enable MPLS globally:
   ```bash
   RouterB(config)# mpls ip
   ```
2. Enable MPLS on interfaces connecting to Router A and Router C:
   ```bash
   RouterB(config)# interface gigabitEthernet 0/0
   RouterB(config-if)# mpls ip
   RouterB(config)# interface gigabitEthernet 0/1
   RouterB(config-if)# mpls ip
   ```

#### **2. Configure MPLS on Router A**
1. Enable MPLS globally:
   ```bash
   RouterA(config)# mpls ip
   ```
2. Enable MPLS on the interface connecting to Router B:
   ```bash
   RouterA(config)# interface gigabitEthernet 0/1
   RouterA(config-if)# mpls ip
   ```

#### **3. Configure MPLS on Router C**
1. Enable MPLS globally:
   ```bash
   RouterC(config)# mpls ip
   ```
2. Enable MPLS on the interface connecting to Router B:
   ```bash
   RouterC(config)# interface gigabitEthernet 0/1
   RouterC(config-if)# mpls ip
   ```

---

### **Step 3: Configure LDP (Label Distribution Protocol)**

1. On all routers, enable LDP:
   ```bash
   Router(config)# mpls label protocol ldp
   ```
2. Verify LDP neighbors:
   ```bash
   Router# show mpls ldp neighbor
   ```
   Example output:
   ```
   Peer LDP Identifier: 192.168.1.1:0; Local LDP Identifier: 192.168.2.1:0
   ```

---

### **Step 4: Configure MPLS Routing**

1. Use OSPF for label-switched path (LSP) creation:
   - Enable OSPF globally:
     ```bash
     Router(config)# router ospf 1
     ```
   - Advertise connected networks:
     ```bash
     Router(config-router)# network 192.168.10.0 0.0.0.255 area 0
     Router(config-router)# network 192.168.20.0 0.0.0.255 area 0
     Router(config-router)# network 192.168.30.0 0.0.0.255 area 0
     Router(config-router)# network 192.168.40.0 0.0.0.255 area 0
     ```

2. Verify OSPF routes:
   ```bash
   Router# show ip ospf neighbor
   ```

---

### **Step 5: Verify MPLS Functionality**

1. Check the MPLS forwarding table:
   ```bash
   Router# show mpls forwarding-table
   ```
   Example output:
   ```
   Local Label  Outgoing Label  Prefix            Next Hop
   16           17              192.168.10.0/24  192.168.1.1
   ```

2. Ping between environments to test MPLS routing:
   - From **PC 1 (Dev)**, ping **PC 4 (Production)**:
     ```bash
     ping 192.168.40.2
     ```
   - Ensure low latency and efficient path selection.

3. Use traceroute to observe label-switched paths:
   ```bash
   traceroute 192.168.40.2
   ```

---

### **Step 6: Troubleshoot MPLS Issues**

1. Check LDP neighbors:
   ```bash
   Router# show mpls ldp neighbor
   ```
2. Verify OSPF adjacency:
   ```bash
   Router# show ip ospf neighbor
   ```
3. Use Wireshark to capture MPLS labels:
   - Filter for MPLS packets:
     ```bash
     mpls
     ```

---

## **Hands-On Challenges**

1. **Simulate MPLS Failures:**
   - Disable the link between Router B and Router C:
     ```bash
     RouterB(config)# interface gigabitEthernet 0/1
     RouterB(config-if)# shutdown
     ```
   - Observe traffic rerouting.

2. **Traffic Engineering with TE Tunnels:**
   - Configure MPLS Traffic Engineering (TE) tunnels for specific paths:
     ```bash
     Router(config)# interface tunnel 0
     Router(config-if)# ip unnumbered loopback0
     Router(config-if)# tunnel mode mpls traffic-eng
     Router(config-if)# tunnel destination 192.168.40.1
     ```

3. **Analyze MPLS Performance:**
   - Measure MPLS performance using tools like `iperf` or SNMP monitoring.

---

## **Expected Outcome**
- MPLS routes traffic efficiently with low latency.
- Label-switched paths are established and verified.
- MPLS handles link failures gracefully, ensuring uninterrupted traffic flow.

---

## **Key Takeaways**
- MPLS optimizes routing by using label switching instead of traditional IP lookups.
- LDP and OSPF are critical components for establishing MPLS functionality.
- Traffic engineering with MPLS ensures efficient resource utilization in large-scale networks.

---
