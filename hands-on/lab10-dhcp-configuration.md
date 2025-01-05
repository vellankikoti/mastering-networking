# Lab 10: Configuring DHCP for Dynamic IP Address Assignment

## **Objective**
- Understand how DHCP automates the assignment of IP addresses, subnet masks, gateways, and DNS servers.
- Configure a router as a DHCP server for the Dev, QA, Stage, and Production environments.
- Verify dynamic IP assignments and troubleshoot DHCP-related issues.

---

## **Scenario**
Your organization needs to automate IP address assignments for the following environments:

| **Environment** | **Network Range**   |
|------------------|---------------------|
| Dev             | 192.168.10.0/24    |
| QA              | 192.168.20.0/24    |
| Stage           | 192.168.30.0/24    |
| Production       | 192.168.40.0/24    |

Each environment should dynamically receive:
- IP address within its respective range.
- Subnet mask, default gateway, and DNS server details.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use the **Lab 7 topology** with VLANs and inter-VLAN routing.
2. Ensure PCs are connected to their respective VLANs on the switch.

---

### **Step 2: Configure the Router as a DHCP Server**

1. Access the router CLI.
2. Enter global configuration mode:
   ```bash
   Router> enable
   Router# configure terminal
   ```

#### **Configure DHCP for Dev:**
1. Define the DHCP pool for Dev:
   ```bash
   Router(config)# ip dhcp pool Dev
   Router(dhcp-config)# network 192.168.10.0 255.255.255.0
   Router(dhcp-config)# default-router 192.168.10.1
   Router(dhcp-config)# dns-server 8.8.8.8
   ```
2. Exclude static IPs (e.g., for servers):
   ```bash
   Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
   ```

#### **Repeat for QA, Stage, and Production:**
1. **QA Pool:**
   ```bash
   Router(config)# ip dhcp pool QA
   Router(dhcp-config)# network 192.168.20.0 255.255.255.0
   Router(dhcp-config)# default-router 192.168.20.1
   Router(dhcp-config)# dns-server 8.8.8.8
   Router(config)# ip dhcp excluded-address 192.168.20.1 192.168.20.10
   ```

2. **Stage Pool:**
   ```bash
   Router(config)# ip dhcp pool Stage
   Router(dhcp-config)# network 192.168.30.0 255.255.255.0
   Router(dhcp-config)# default-router 192.168.30.1
   Router(dhcp-config)# dns-server 8.8.8.8
   Router(config)# ip dhcp excluded-address 192.168.30.1 192.168.30.10
   ```

3. **Production Pool:**
   ```bash
   Router(config)# ip dhcp pool Production
   Router(dhcp-config)# network 192.168.40.0 255.255.255.0
   Router(dhcp-config)# default-router 192.168.40.1
   Router(dhcp-config)# dns-server 8.8.8.8
   Router(config)# ip dhcp excluded-address 192.168.40.1 192.168.40.10
   ```

---

### **Step 3: Test DHCP Assignments**
1. Configure PCs to use DHCP:
   - Open the **Desktop** tab on each PC.
   - Set the IP configuration to **DHCP**.
2. Verify IP assignment:
   - Open the command prompt on each PC.
   - Run:
     ```bash
     ipconfig
     ```
   - Example output for PC 1 (Dev):
     ```
     IP Address: 192.168.10.11
     Subnet Mask: 255.255.255.0
     Default Gateway: 192.168.10.1
     DNS Server: 8.8.8.8
     ```

---

### **Step 4: Verify DHCP Configuration**
1. On the router, display DHCP bindings:
   ```bash
   Router# show ip dhcp binding
   ```
2. Example output:
   ```
   IP Address       Client-ID/Lease Expiry        Type
   192.168.10.11    0100.1234.5678.90            Automatic
   192.168.20.11    0100.1234.5678.91            Automatic
   ```

---

### **Step 5: Troubleshoot DHCP Issues**
1. **No IP Address Assigned:**
   - Verify DHCP pool configurations:
     ```bash
     Router# show running-config | include dhcp
     ```
   - Check if the DHCP service is enabled:
     ```bash
     Router# show ip dhcp server statistics
     ```

2. **IP Conflicts:**
   - Ensure excluded addresses don’t overlap with the DHCP pool.

---

## **Hands-On Challenges**
1. **Add a New Environment:**
   - Create a new VLAN and assign a DHCP pool for it.
2. **Simulate Lease Expiry:**
   - Reduce the lease time for DHCP:
     ```bash
     Router(dhcp-config)# lease 0 2 0
     ```
   - Verify dynamic reassignments after 2 hours.
3. **Capture DHCP Traffic:**
   - Use Wireshark to capture DHCP DISCOVER, OFFER, REQUEST, and ACK packets.

---

## **Expected Outcome**
- PCs dynamically receive IP addresses, subnet masks, gateways, and DNS servers.
- IP conflicts and manual configuration issues are eliminated.
- DHCP functionality is verified across all environments.

---

## **Key Takeaways**
- DHCP simplifies network administration by automating IP assignments.
- Excluded addresses ensure reserved IPs are not assigned dynamically.
- Configuring DHCP pools for multiple environments aligns with organizational needs.

---
