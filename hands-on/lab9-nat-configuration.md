# Lab 9: Configuring NAT (Network Address Translation)

## **Objective**
- Understand how NAT enables private networks to communicate with the internet.
- Configure Static NAT, Dynamic NAT, and PAT (Port Address Translation).
- Apply NAT to the Dev, QA, Stage, and Production environments.

---

## **Scenario**
Your organization has the following private networks:
1. **Dev (192.168.10.0/24)**
2. **QA (192.168.20.0/24)**
3. **Stage (192.168.30.0/24)**
4. **Production (192.168.40.0/24)**

Only one public IP address (`203.0.113.1`) is available. You need to configure NAT to allow internet access for all environments while conserving the public IP.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use the **Lab 7 topology** with VLANs and inter-VLAN routing.
2. Add a simulated **ISP router** and connect it to the router via a new interface.

---

### **Step 2: Configure NAT on the Router**

#### **1. Static NAT:**
Static NAT maps a single private IP to a single public IP.

1. Map the Dev server’s IP (`192.168.10.10`) to the public IP:
   ```bash
   Router(config)# ip nat inside source static 192.168.10.10 203.0.113.2
   ```
2. Define inside and outside interfaces:
   ```bash
   Router(config)# interface gigabitEthernet 0/0
   Router(config-if)# ip nat inside
   Router(config-if)# exit
   Router(config)# interface gigabitEthernet 0/1
   Router(config-if)# ip nat outside
   Router(config-if)# exit
   ```

#### **2. Dynamic NAT:**
Dynamic NAT maps multiple private IPs to a pool of public IPs.

1. Create a NAT pool:
   ```bash
   Router(config)# ip nat pool public-pool 203.0.113.10 203.0.113.20 netmask 255.255.255.0
   ```
2. Configure an access list to identify private IPs:
   ```bash
   Router(config)# access-list 1 permit 192.168.20.0 0.0.0.255
   ```
3. Bind the access list to the NAT pool:
   ```bash
   Router(config)# ip nat inside source list 1 pool public-pool
   ```

#### **3. PAT (Port Address Translation):**
PAT maps multiple private IPs to a single public IP using unique port numbers.

1. Configure PAT using the public IP:
   ```bash
   Router(config)# ip nat inside source list 1 interface gigabitEthernet 0/1 overload
   ```

---

### **Step 3: Verify NAT Configuration**
1. Display NAT translations:
   ```bash
   Router# show ip nat translations
   ```
2. Example output:
   ```
   Pro Inside global      Inside local       Outside local      Outside global
   --- 203.0.113.2        192.168.10.10      ---                ---
   --- 203.0.113.10       192.168.20.2       ---                ---
   ```

---

### **Step 4: Test NAT Functionality**
1. From **PC 1 (Dev)**, ping an external IP (e.g., `8.8.8.8`):
   ```bash
   ping 8.8.8.8
   ```
   - Expected output:
     ```
     Reply from 8.8.8.8: bytes=32 time<1ms TTL=64
     ```

2. From **PC 2 (QA)**, initiate a web request:
   - Open a browser and access `http://203.0.113.1`.
   - Ensure the request is successfully routed through NAT.

---

## **Hands-On Challenges**
1. **Add More NAT Rules:**
   - Map a specific port (e.g., SSH) from the public IP to a private IP:
     ```bash
     Router(config)# ip nat inside source static tcp 192.168.10.10 22 203.0.113.1 22
     ```

2. **Monitor NAT Behavior:**
   - Use Wireshark on the outside interface to capture NAT-translated traffic.

3. **Simulate NAT Failures:**
   - Remove the NAT configuration for QA and observe failed connectivity:
     ```bash
     Router(config)# no ip nat inside source list 1 pool public-pool
     ```

---

## **Expected Outcome**
- All private networks (Dev, QA, Stage, Production) access the internet through NAT.
- PAT conserves public IP addresses by utilizing ports.
- Connectivity is verified, and troubleshooting is conducted effectively.

---

## **Key Takeaways**
- **Static NAT:** Directly maps private to public IPs for predictable access.
- **Dynamic NAT:** Uses a pool of public IPs for private IPs dynamically.
- **PAT:** Maps multiple private IPs to a single public IP, conserving resources.
- NAT is essential for enabling private networks to communicate externally while securing internal addresses.

---
