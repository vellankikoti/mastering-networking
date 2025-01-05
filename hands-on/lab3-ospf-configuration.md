# Lab 3: Configuring OSPF in a Multi-Area Network

## **Objective**
- Set up OSPF for inter-area communication in a network with multiple environments (Dev, QA, Stage, Production).
- Understand OSPF neighbor relationships and area-based design.

---

## **Scenario**
Your organization’s network spans four environments:
1. **Dev (Area 1):** 192.168.10.0/24
2. **QA (Area 2):** 192.168.20.0/24
3. **Stage (Area 3):** 192.168.30.0/24
4. **Production (Backbone, Area 0):** 192.168.40.0/24

You need to configure OSPF on routers to ensure seamless communication across these environments.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add four routers and connect them as follows:
   - **Router A:** Connects Dev (Area 1) and Production (Area 0).
   - **Router B:** Connects QA (Area 2) and Production (Area 0).
   - **Router C:** Connects Stage (Area 3) and Production (Area 0).
   - **Router D:** Backbone router for Area 0.
3. Add PCs to each environment:
   - **PC 1 (Dev):** Connected to Router A.
   - **PC 2 (QA):** Connected to Router B.
   - **PC 3 (Stage):** Connected to Router C.
   - **PC 4 (Production):** Connected to Router D.

---

### **Step 2: Configure OSPF on Routers**

#### **Router A (Dev + Area 0):**
1. Enable OSPF:
   ```bash
   Router(config)# router ospf 1
   ```
2. Configure Area 1 and Area 0 networks:
   ```bash
   Router(config-router)# network 192.168.10.0 0.0.0.255 area 1
   Router(config-router)# network 192.168.40.0 0.0.0.255 area 0
   ```

#### **Router B (QA + Area 0):**
1. Enable OSPF:
   ```bash
   Router(config)# router ospf 1
   ```
2. Configure Area 2 and Area 0 networks:
   ```bash
   Router(config-router)# network 192.168.20.0 0.0.0.255 area 2
   Router(config-router)# network 192.168.40.0 0.0.0.255 area 0
   ```

#### **Router C (Stage + Area 0):**
1. Enable OSPF:
   ```bash
   Router(config)# router ospf 1
   ```
2. Configure Area 3 and Area 0 networks:
   ```bash
   Router(config-router)# network 192.168.30.0 0.0.0.255 area 3
   Router(config-router)# network 192.168.40.0 0.0.0.255 area 0
   ```

#### **Router D (Backbone):**
1. Enable OSPF:
   ```bash
   Router(config)# router ospf 1
   ```
2. Configure the Backbone (Area 0) network:
   ```bash
   Router(config-router)# network 192.168.40.0 0.0.0.255 area 0
   ```

---

### **Step 3: Verify OSPF Neighbors**
1. On each router, check OSPF neighbors:
   ```bash
   Router# show ip ospf neighbor
   ```
2. Example output on Router A:
   ```
   Neighbor ID     Pri   State           Dead Time   Address         Interface
   192.168.40.2     1    FULL/DR         00:00:32    192.168.40.1    Gig0/1
   ```

---

### **Step 4: Verify Routes**
1. Display the routing table on Router A:
   ```bash
   Router# show ip route
   ```
2. Example output:
   ```
   O   192.168.20.0/24 [110/10] via 192.168.40.2, 00:00:30, Gig0/1
   O   192.168.30.0/24 [110/20] via 192.168.40.3, 00:00:40, Gig0/1
   ```

---

### **Step 5: Test Connectivity**
1. From **PC 1 (Dev)**, ping **PC 4 (Production)**:
   ```bash
   ping 192.168.40.2
   ```
2. Expected output:
   ```
   Reply from 192.168.40.2: bytes=32 time<1ms TTL=64
   ```

---

## **Hands-On Challenges**
1. **Simulate Network Failure:**
   - Shut down an interface on Router A and observe how OSPF recalculates routes:
     ```bash
     Router(config)# interface gig0/0
     Router(config-if)# shutdown
     ```
   - Use `show ip route` to verify the updated routing table.

2. **Advanced Area Design:**
   - Add a stub area (Area 4) connected to Router C.
   - Configure the stub area:
     ```bash
     Router(config-router)# area 4 stub
     ```

---

## **Expected Outcome**
- OSPF establishes neighbor relationships between routers.
- All PCs in Dev, QA, Stage, and Production environments communicate seamlessly.
- Routes automatically adjust to network changes.

---

## **Key Takeaways**
- OSPF’s area-based design optimizes routing in large networks.
- Backbone (Area 0) is essential for inter-area communication.
- Understanding OSPF neighbor relationships and route calculation simplifies troubleshooting.

---
