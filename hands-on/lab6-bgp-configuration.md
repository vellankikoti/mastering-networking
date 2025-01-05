# Lab 6: Configuring BGP for Internet Routing

## **Objective**
- Configure BGP in a network with multiple autonomous systems (AS).
- Understand BGP attributes and how they influence route selection.
- Explore real-world use cases of BGP for inter-AS communication.

---

## **Scenario**
Your organization spans two autonomous systems:
1. **AS 65001** (Dev and QA environments).
2. **AS 65002** (Stage and Production environments).

You need to configure BGP to establish communication between these ASes while controlling the preferred routes using BGP attributes.

| **Environment** | **Network Range** | **AS Number** |
|------------------|-------------------|---------------|
| Dev             | 192.168.10.0/24  | 65001         |
| QA              | 192.168.20.0/24  | 65001         |
| Stage           | 192.168.30.0/24  | 65002         |
| Production       | 192.168.40.0/24  | 65002         |

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add four routers:
   - **Router A (AS 65001):** Connects Dev and QA.
   - **Router B (AS 65001):** Connects to Router C for inter-AS communication.
   - **Router C (AS 65002):** Connects Stage and Production.
   - **Router D (AS 65002):** Connects Stage and Router B for inter-AS communication.
3. Connect PCs to each router as per the environment.

---

### **Step 2: Configure BGP on Routers**

#### **Router A (AS 65001):**
1. Enable BGP and define the AS number:
   ```bash
   Router(config)# router bgp 65001
   ```
2. Advertise the Dev and QA networks:
   ```bash
   Router(config-router)# network 192.168.10.0 mask 255.255.255.0
   Router(config-router)# network 192.168.20.0 mask 255.255.255.0
   ```
3. Establish a neighbor relationship with Router B:
   ```bash
   Router(config-router)# neighbor 192.168.30.1 remote-as 65001
   ```

#### **Router B (AS 65001):**
1. Enable BGP and define the AS number:
   ```bash
   Router(config)# router bgp 65001
   ```
2. Advertise connected networks and set up a neighbor relationship with Router C:
   ```bash
   Router(config-router)# neighbor 192.168.30.2 remote-as 65002
   ```

#### **Router C (AS 65002):**
1. Enable BGP and define the AS number:
   ```bash
   Router(config)# router bgp 65002
   ```
2. Advertise Stage and Production networks:
   ```bash
   Router(config-router)# network 192.168.30.0 mask 255.255.255.0
   Router(config-router)# network 192.168.40.0 mask 255.255.255.0
   ```
3. Establish a neighbor relationship with Router B:
   ```bash
   Router(config-router)# neighbor 192.168.20.2 remote-as 65001
   ```

---

### **Step 3: Verify BGP Neighbors**
1. Check BGP neighbors on Router A:
   ```bash
   Router# show ip bgp neighbors
   ```
2. Example output:
   ```
   BGP neighbor is 192.168.30.1, remote AS 65001
   BGP state = Established, up for 00:10:23
   ```

---

### **Step 4: Verify Routes**
1. Display the routing table on Router A:
   ```bash
   Router# show ip route
   ```
2. Example output:
   ```
   B   192.168.40.0/24 [20/0] via 192.168.30.1, 00:00:23
   B   192.168.30.0/24 [20/0] via 192.168.30.1, 00:00:23
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

## **BGP Attributes**
| **Attribute**         | **Description**                                  |
|------------------------|-------------------------------------------------|
| **AS Path**           | Lists the autonomous systems a route traverses. |
| **Next Hop**          | Indicates the next router in the path.          |
| **Local Preference**  | Determines the preferred route for outgoing traffic. |
| **MED (Metric)**      | Suggests the preferred entry point for incoming traffic. |

---

## **Hands-On Challenges**
1. **Control Route Preference:**
   - Modify the **Local Preference** on Router A to prefer routes through Router D:
     ```bash
     Router(config-router)# neighbor 192.168.30.1 route-map PREFER_LOCAL in
     ```

2. **Simulate Network Failures:**
   - Shut down an interface on Router C and observe BGP recalculating routes:
     ```bash
     Router(config)# interface gig0/1
     Router(config-if)# shutdown
     ```

3. **Analyze BGP with Wireshark:**
   - Capture and analyze BGP packets using:
     ```bash
     tcp.port == 179
     ```

---

## **Expected Outcome**
- BGP establishes neighbor relationships between AS 65001 and AS 65002.
- Routes are dynamically calculated based on BGP attributes.
- Traffic between environments flows seamlessly, even during network changes.

---

## **Key Takeaways**
- BGP is essential for managing inter-AS communication on the internet.
- Attributes like AS Path and Local Preference influence route selection.
- Hands-on experience with BGP prepares you for large-scale network deployments.

---
