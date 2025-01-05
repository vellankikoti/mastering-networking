# Lab 11: Configuring HSRP (Hot Standby Router Protocol)

## **Objective**
- Understand how HSRP ensures high availability and redundancy.
- Configure two routers to act as primary and standby for failover.
- Test failover scenarios to validate HSRP functionality.

---

## **Scenario**
Your organization requires redundancy for the gateway to ensure uninterrupted access. Two routers will be configured using HSRP:
1. **Router A:** Primary router with priority.
2. **Router B:** Standby router for failover.

The following VLANs will be configured:
- **Dev (192.168.10.0/24)**
- **QA (192.168.20.0/24)**
- **Stage (192.168.30.0/24)**
- **Production (192.168.40.0/24)**

HSRP will provide a virtual IP for each VLAN to act as the default gateway.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add two routers (Router A and Router B).
3. Connect the routers to a Layer 2 switch.
4. Add PCs to the switch, assigning them to respective VLANs.

---

### **Step 2: Configure HSRP on Routers**

#### **Router A (Primary):**
1. Access Router A CLI.
2. Configure interfaces for each VLAN:
   - **VLAN 10 (Dev):**
     ```bash
     RouterA(config)# interface gigabitEthernet 0/0.10
     RouterA(config-if)# encapsulation dot1Q 10
     RouterA(config-if)# ip address 192.168.10.2 255.255.255.0
     RouterA(config-if)# standby 10 ip 192.168.10.1
     RouterA(config-if)# standby 10 priority 110
     RouterA(config-if)# standby 10 preempt
     ```
   - Repeat for VLAN 20, 30, and 40 with respective subinterfaces.

#### **Router B (Standby):**
1. Access Router B CLI.
2. Configure interfaces for each VLAN:
   - **VLAN 10 (Dev):**
     ```bash
     RouterB(config)# interface gigabitEthernet 0/0.10
     RouterB(config-if)# encapsulation dot1Q 10
     RouterB(config-if)# ip address 192.168.10.3 255.255.255.0
     RouterB(config-if)# standby 10 ip 192.168.10.1
     ```
   - Repeat for VLAN 20, 30, and 40 with respective subinterfaces.

---

### **Step 3: Verify HSRP Configuration**
1. On Router A:
   ```bash
   RouterA# show standby brief
   ```
   Example output:
   ```
   Interface   Grp  Pri  State    Active     Standby   Virtual IP
   Gi0/0.10    10   110  Active   local      192.168.10.3  192.168.10.1
   ```

2. On Router B:
   ```bash
   RouterB# show standby brief
   ```
   Example output:
   ```
   Interface   Grp  Pri  State    Active     Standby   Virtual IP
   Gi0/0.10    10   100  Standby  192.168.10.2  local   192.168.10.1
   ```

---

### **Step 4: Test HSRP Failover**
1. From **PC 1 (Dev)**, ping the default gateway:
   ```bash
   ping 192.168.10.1
   ```
   - Expected output:
     ```
     Reply from 192.168.10.1: bytes=32 time<1ms TTL=64
     ```

2. Simulate a failure on Router A:
   - Shut down the subinterface on Router A:
     ```bash
     RouterA(config)# interface gigabitEthernet 0/0.10
     RouterA(config-if)# shutdown
     ```
3. Verify Router B becomes active:
   ```bash
   RouterB# show standby brief
   ```
   Example output:
   ```
   Interface   Grp  Pri  State    Active     Standby   Virtual IP
   Gi0/0.10    10   100  Active   local      192.168.10.2  192.168.10.1
   ```

4. Re-enable the interface on Router A:
   ```bash
   RouterA(config)# interface gigabitEthernet 0/0.10
   RouterA(config-if)# no shutdown
   ```

---

### **Step 5: Capture and Analyze HSRP Traffic**
1. Use Wireshark to capture HSRP packets.
2. Filter HSRP traffic:
   ```bash
   udp.port == 1985
   ```
3. Observe HSRP hello and state-change packets.

---

## **Hands-On Challenges**
1. **Modify Priority:**
   - Increase Router B’s priority and observe failover behavior.
     ```bash
     RouterB(config-if)# standby 10 priority 120
     ```

2. **Multiple HSRP Groups:**
   - Configure separate HSRP groups for each VLAN and validate failover.

3. **Advanced HSRP Features:**
   - Configure HSRP authentication:
     ```bash
     RouterA(config-if)# standby 10 authentication md5 key-string myKey
     ```

---

## **Expected Outcome**
- HSRP provides seamless failover between Router A and Router B.
- PCs maintain connectivity during router failures.
- Wireshark captures validate HSRP operations.

---

## **Key Takeaways**
- HSRP ensures high availability by creating a virtual gateway.
- Priority settings determine active and standby roles.
- Failover is automatic and minimizes downtime in enterprise networks.

---
