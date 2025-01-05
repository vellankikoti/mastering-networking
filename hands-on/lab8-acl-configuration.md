# Lab 8: Configuring Access Control Lists (ACLs)

## **Objective**
- Understand how ACLs filter traffic based on predefined rules.
- Configure standard and extended ACLs to control traffic between environments (Dev, QA, Stage, Production).
- Apply ACLs to enhance network security and enforce policies.

---

## **Scenario**
Your organization requires the following traffic filtering:
1. **Allow only Dev (192.168.10.0/24) to access QA (192.168.20.0/24).**
2. **Deny traffic from Stage (192.168.30.0/24) to Production (192.168.40.0/24).**
3. **Allow all other traffic.**

You will implement these rules using standard and extended ACLs on the router.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use the **Lab 7 topology** with VLANs and inter-VLAN routing.
2. Ensure all VLANs and subinterfaces are configured as per the previous lab.

---

### **Step 2: Configure a Standard ACL**

#### **Objective: Allow Dev to Access QA**
1. Access the router CLI.
2. Create a standard ACL:
   ```bash
   Router(config)# access-list 10 permit 192.168.10.0 0.0.0.255
   ```
3. Apply the ACL to the inbound traffic on the QA subinterface:
   ```bash
   Router(config)# interface gigabitEthernet 0/0.20
   Router(config-if)# ip access-group 10 in
   ```

---

### **Step 3: Configure an Extended ACL**

#### **Objective: Deny Stage Access to Production**
1. Create an extended ACL:
   ```bash
   Router(config)# access-list 100 deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255
   Router(config)# access-list 100 permit ip any any
   ```
2. Apply the ACL to the outbound traffic on the Production subinterface:
   ```bash
   Router(config)# interface gigabitEthernet 0/0.40
   Router(config-if)# ip access-group 100 out
   ```

---

### **Step 4: Verify ACL Configuration**
1. Check active ACLs:
   ```bash
   Router# show access-lists
   ```
2. Example output:
   ```
   Standard IP access list 10
       10 permit 192.168.10.0 0.0.0.255
   Extended IP access list 100
       10 deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255
       20 permit ip any any
   ```

---

### **Step 5: Test Connectivity**
1. From **PC 1 (Dev)**, ping **PC 2 (QA)**:
   ```bash
   ping 192.168.20.2
   ```
   - Expected output:
     ```
     Reply from 192.168.20.2: bytes=32 time<1ms TTL=64
     ```

2. From **PC 3 (Stage)**, ping **PC 4 (Production)**:
   ```bash
   ping 192.168.40.2
   ```
   - Expected output:
     ```
     Request timed out.
     ```

3. From **PC 3 (Stage)**, ping **PC 1 (Dev)**:
   ```bash
   ping 192.168.10.2
   ```
   - Expected output:
     ```
     Reply from 192.168.10.2: bytes=32 time<1ms TTL=64
     ```

---

## **Hands-On Challenges**
1. **Implement More ACL Rules:**
   - Deny traffic from QA (192.168.20.0/24) to Stage (192.168.30.0/24).
   - Permit all traffic from Production (192.168.40.0/24) to all environments.

2. **Monitor Traffic with Wireshark:**
   - Capture and analyze traffic allowed and denied by ACLs.

3. **Simulate Real-World Policies:**
   - Restrict specific ports (e.g., deny HTTP traffic on port 80):
     ```bash
     access-list 101 deny tcp any any eq 80
     ```

---

## **Expected Outcome**
- ACLs are applied successfully to control traffic between environments.
- Only authorized communication is allowed, and unauthorized traffic is blocked.
- ACL rules are verified with connectivity tests and monitored with Wireshark.

---

## **Key Takeaways**
- Standard ACLs filter traffic based on source IP addresses.
- Extended ACLs provide granular control over source, destination, and protocols.
- ACLs are essential for enforcing security policies in enterprise networks.

---
