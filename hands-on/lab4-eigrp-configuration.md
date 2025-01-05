# Lab 4: Configuring EIGRP for a Scalable Network

## **Objective**
- Set up EIGRP in a network with multiple environments (Dev, QA, Stage, Production).
- Understand how EIGRP calculates metrics and handles neighbor relationships.
- Compare EIGRP and OSPF in terms of configuration, functionality, and performance.

---

## **Scenario**
Your organization is transitioning from static routing to dynamic routing using EIGRP. You need to:
1. Configure EIGRP for four environments:
   - **Dev:** 192.168.10.0/24
   - **QA:** 192.168.20.0/24
   - **Stage:** 192.168.30.0/24
   - **Production:** 192.168.40.0/24
2. Ensure seamless communication across all environments with optimized routing.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add four routers connected in a square topology:
   - Router A connects Dev and QA.
   - Router B connects QA and Production.
   - Router C connects Production and Stage.
   - Router D connects Stage and Dev.
3. Add PCs to each environment as done in Lab 3.

---

### **Step 2: Configure EIGRP on Routers**

#### **Router A:**
1. Enable EIGRP:
   ```bash
   Router(config)# router eigrp 100
   ```
   - `100` is the autonomous system (AS) number.
2. Configure networks:
   ```bash
   Router(config-router)# network 192.168.10.0
   Router(config-router)# network 192.168.20.0
   ```

#### **Router B:**
1. Enable EIGRP:
   ```bash
   Router(config)# router eigrp 100
   ```
2. Configure networks:
   ```bash
   Router(config-router)# network 192.168.20.0
   Router(config-router)# network 192.168.40.0
   ```

#### **Router C:**
1. Enable EIGRP:
   ```bash
   Router(config)# router eigrp 100
   ```
2. Configure networks:
   ```bash
   Router(config-router)# network 192.168.40.0
   Router(config-router)# network 192.168.30.0
   ```

#### **Router D:**
1. Enable EIGRP:
   ```bash
   Router(config)# router eigrp 100
   ```
2. Configure networks:
   ```bash
   Router(config-router)# network 192.168.30.0
   Router(config-router)# network 192.168.10.0
   ```

---

### **Step 3: Verify EIGRP Neighbors**
1. Check EIGRP neighbors on Router A:
   ```bash
   Router# show ip eigrp neighbors
   ```
2. Expected output:
   ```
   Address         Interface        Hold  Uptime   SRTT   RTO   Q  Seq
   192.168.20.2    Gig0/1           12    00:10:12  16    100   0  24
   ```

---

### **Step 4: Verify Routes**
1. Display the routing table on Router A:
   ```bash
   Router# show ip route
   ```
2. Example output:
   ```
   D   192.168.30.0/24 [90/156160] via 192.168.20.2, 00:00:10, Gig0/1
   D   192.168.40.0/24 [90/30720] via 192.168.20.2, 00:00:10, Gig0/1
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

## **EIGRP vs. OSPF: Key Differences**

| **Feature**         | **EIGRP**                                   | **OSPF**                                   |
|----------------------|---------------------------------------------|--------------------------------------------|
| **Type**            | Hybrid Protocol (Distance-vector + Link-state). | Pure Link-State Protocol.                  |
| **Metric**          | Bandwidth, Delay, Load, Reliability.         | Cost (based on bandwidth).                 |
| **Convergence**     | Faster due to DUAL algorithm.                | Slower in large networks.                  |
| **Area Design**     | Not area-based; flat architecture.           | Area-based (Backbone and others).          |
| **Resource Usage**  | Lower CPU and memory.                        | Higher CPU and memory usage.               |
| **Scalability**     | Best for medium to large networks.           | Better for large, complex networks.        |

---

## **Hands-On Challenges**
1. **Simulate Network Failure:**
   - Disconnect a link between Router A and Router B.
   - Observe how EIGRP recalculates routes using:
     ```bash
     Router# show ip route
     ```
2. **Modify EIGRP Metrics:**
   - Adjust the delay metric on Router A:
     ```bash
     Router(config)# interface gig0/0
     Router(config-if)# delay 500
     ```
   - Verify how the new metric affects route selection.

3. **Compare OSPF and EIGRP Outputs:**
   - Configure OSPF in the same topology as EIGRP.
   - Compare `show ip route` outputs and neighbor relationships.

---

## **Expected Outcome**
- EIGRP dynamically establishes routes and maintains neighbor relationships.
- Adjusting metrics affects route selection.
- Comparing OSPF and EIGRP highlights their strengths and use cases.

---

## **Key Takeaways**
- EIGRP is an efficient and scalable protocol for medium-sized networks.
- OSPF is better suited for large, complex networks with multiple areas.
- Hands-on experience with both protocols provides insights into their real-world applications.

---
