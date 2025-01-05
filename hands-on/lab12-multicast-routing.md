# Lab 12: Configuring Multicast Routing

## **Objective**
- Understand multicast routing and its efficiency in data distribution.
- Configure PIM (Protocol Independent Multicast) for multicast traffic.
- Test multicast functionality across Dev, QA, Stage, and Production environments.

---

## **Scenario**
Your organization requires efficient data distribution from a multicast source (e.g., video server) to multiple recipients across environments:

| **Environment** | **Network Range**   |
|------------------|---------------------|
| Dev             | 192.168.10.0/24    |
| QA              | 192.168.20.0/24    |
| Stage           | 192.168.30.0/24    |
| Production       | 192.168.40.0/24    |

You need to configure multicast routing using PIM to ensure efficient delivery without flooding unnecessary traffic.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add three routers:
   - **Router A:** Connects Dev and QA.
   - **Router B:** Acts as a core router connecting Router A and Router C.
   - **Router C:** Connects Stage and Production.
3. Add PCs to each VLAN for testing multicast reception.

---

### **Step 2: Configure Multicast Routing on All Routers**

#### **Enable Multicast Routing:**
1. On each router, enable multicast routing:
   ```bash
   Router(config)# ip multicast-routing
   ```

#### **Configure PIM on Interfaces:**
1. On each router, enable PIM sparse mode on all interfaces:
   ```bash
   Router(config)# interface gigabitEthernet 0/0
   Router(config-if)# ip pim sparse-mode
   ```

2. Repeat for all interfaces connecting to other routers and networks.

#### **Configure RP (Rendezvous Point):**
1. Designate Router B as the Rendezvous Point:
   ```bash
   RouterB(config)# ip pim rp-address 192.168.20.1
   ```

---

### **Step 3: Configure Multicast Source and Receivers**
1. On **PC 1 (Dev)**, simulate a multicast receiver:
   - Open the command prompt and join a multicast group:
     ```bash
     ip add multicast 239.1.1.1
     ```

2. On **PC 3 (Stage)**, configure the multicast source:
   - Start a multicast video stream:
     ```bash
     stream --multicast-address=239.1.1.1 --interface=192.168.30.2
     ```

---

### **Step 4: Verify Multicast Routing**
1. Check the multicast routing table on Router A:
   ```bash
   RouterA# show ip mroute
   ```
   Example output:
   ```
   (*, 239.1.1.1), RP 192.168.20.1, uptime 00:05:00, flags: S
   Incoming interface: GigabitEthernet0/1, RPF nbr 192.168.20.1
   Outgoing interface list:
     GigabitEthernet0/0, uptime 00:05:00, flags: F
   ```

2. Verify PIM neighbors:
   ```bash
   RouterA# show ip pim neighbor
   ```
   Example output:
   ```
   Neighbor Address  Interface           Uptime    Expires   Mode
   192.168.20.2      GigabitEthernet0/1  00:05:00  00:01:45  Dense
   ```

---

### **Step 5: Test Multicast Functionality**
1. From **PC 1 (Dev)**, test multicast reception:
   - Open a video player that supports multicast and play the stream from `239.1.1.1`.

2. From **PC 2 (QA)**, verify reception by joining the multicast group:
   ```bash
   ip add multicast 239.1.1.1
   ```

3. Ensure PCs in Stage and Production can also receive the multicast stream.

---

### **Hands-On Challenges**
1. **Simulate RP Failure:**
   - Shut down the Rendezvous Point (Router B):
     ```bash
     RouterB(config)# interface gigabitEthernet 0/1
     RouterB(config-if)# shutdown
     ```
   - Observe traffic rerouting and re-establishment of multicast streams.

2. **Advanced Filtering:**
   - Configure an access list to restrict specific multicast groups:
     ```bash
     Router(config)# ip pim accept-register list 1
     Router(config)# access-list 1 permit 239.1.1.1
     ```

3. **Capture Multicast Traffic:**
   - Use Wireshark to capture and analyze multicast packets:
     ```bash
     ip.dst == 239.1.1.1
     ```

---

## **Expected Outcome**
- Multicast traffic is efficiently distributed to PCs in Dev, QA, Stage, and Production environments.
- Failover and filtering configurations are tested successfully.
- Wireshark captures validate multicast delivery.

---

## **Key Takeaways**
- Multicast reduces bandwidth usage by sending data only to subscribed recipients.
- PIM is a critical protocol for managing multicast routing.
- Understanding multicast routing ensures efficient delivery in enterprise networks.

---
