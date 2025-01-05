# Lab 1: Router Setup and Basic Configuration with Multiple Environments

## **Objective**
- Set up a router in a simulated network.
- Configure basic settings like hostname, IP addresses, and routing for **Dev**, **QA**, **Stage**, and **Production** environments.
- Verify connectivity between the environments.

---

## **Scenario**
Your organization has four environments—Dev, QA, Stage, and Production—on separate networks. You need to set up a router to connect these environments while maintaining logical separation.

| **Environment** | **Network Range**     |
|------------------|-----------------------|
| Dev             | 192.168.10.0/24      |
| QA              | 192.168.20.0/24      |
| Stage           | 192.168.30.0/24      |
| Production       | 192.168.40.0/24      |

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Open Cisco Packet Tracer or GNS3.
2. Add the following devices to the workspace:
   - 1 Router (e.g., Cisco 2911).
   - 4 PCs (one for each environment).
3. Connect the devices:
   - PC 1 (Dev) to Router interface Gig0/0.
   - PC 2 (QA) to Router interface Gig0/1.
   - PC 3 (Stage) to Router interface Gig0/2.
   - PC 4 (Production) to Router interface Gig0/3.

---

### **Step 2: Configure the Router**
1. Access the router’s CLI.
2. Enter privileged mode:
   ```bash
   Router> enable
   ```
3. Enter global configuration mode:
   ```bash
   Router# configure terminal
   ```
4. Set the hostname:
   ```bash
   Router(config)# hostname CorpRouter
   ```
5. Assign IP addresses to the interfaces:
   - For **Dev** (Gig0/0):
     ```bash
     CorpRouter(config)# interface gigabitEthernet 0/0
     CorpRouter(config-if)# ip address 192.168.10.1 255.255.255.0
     CorpRouter(config-if)# no shutdown
     ```
   - For **QA** (Gig0/1):
     ```bash
     CorpRouter(config)# interface gigabitEthernet 0/1
     CorpRouter(config-if)# ip address 192.168.20.1 255.255.255.0
     CorpRouter(config-if)# no shutdown
     ```
   - For **Stage** (Gig0/2):
     ```bash
     CorpRouter(config)# interface gigabitEthernet 0/2
     CorpRouter(config-if)# ip address 192.168.30.1 255.255.255.0
     CorpRouter(config-if)# no shutdown
     ```
   - For **Production** (Gig0/3):
     ```bash
     CorpRouter(config)# interface gigabitEthernet 0/3
     CorpRouter(config-if)# ip address 192.168.40.1 255.255.255.0
     CorpRouter(config-if)# no shutdown
     ```
6. Exit configuration mode:
   ```bash
   CorpRouter(config-if)# exit
   CorpRouter(config)# exit
   ```

---

### **Step 3: Configure the PCs**
1. Assign static IP addresses to the PCs:
   - **PC 1 (Dev):**
     - IP: `192.168.10.2`
     - Subnet Mask: `255.255.255.0`
     - Default Gateway: `192.168.10.1`
   - **PC 2 (QA):**
     - IP: `192.168.20.2`
     - Subnet Mask: `255.255.255.0`
     - Default Gateway: `192.168.20.1`
   - **PC 3 (Stage):**
     - IP: `192.168.30.2`
     - Subnet Mask: `255.255.255.0`
     - Default Gateway: `192.168.30.1`
   - **PC 4 (Production):**
     - IP: `192.168.40.2`
     - Subnet Mask: `255.255.255.0`
     - Default Gateway: `192.168.40.1`

---

### **Step 4: Verify Configuration**
1. Test connectivity between environments:
   - From PC 1 (Dev), ping PC 2 (QA):
     ```bash
     ping 192.168.20.2
     ```
   - From PC 3 (Stage), ping PC 4 (Production):
     ```bash
     ping 192.168.40.2
     ```
2. Expected output for successful pings:
   ```
   Pinging 192.168.20.2 with 32 bytes of data:
   Reply from 192.168.20.2: bytes=32 time<1ms TTL=64
   ```

---

## **Hands-On Challenges**
1. **Enable Inter-Environment Communication:**
   - Allow communication between all four environments.
   - Verify using pings from each PC to others.

2. **Introduce VLANs (Advanced):**
   - Assign VLANs for each environment.
   - Configure router-on-a-stick to manage inter-VLAN routing.

---

## **Expected Outcome**
- PCs in each environment communicate through the router.
- Logical separation is maintained while enabling controlled communication.

---

## **Key Takeaways**
- Simulating real-world environments (Dev, QA, Stage, Production) makes networking concepts relatable.
- Basic router configuration ensures inter-network communication.
- Logical separation and gateway assignment are critical for effective network design.

---
