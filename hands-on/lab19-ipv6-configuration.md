# Lab 19: Configuring IPv6 Networks

## **Objective**
- Understand IPv6 address types and their structure.
- Configure IPv6 addressing and routing on network devices.
- Test and verify IPv6 connectivity across environments.

---

## **Scenario**
Your organization is transitioning to IPv6 to address scalability and future network needs. The following environments require IPv6 configuration:

| **Environment** | **IPv6 Network Range**       |
|------------------|-----------------------------|
| Dev             | 2001:db8:10::/64           |
| QA              | 2001:db8:20::/64           |
| Stage           | 2001:db8:30::/64           |
| Production       | 2001:db8:40::/64           |

You will configure IPv6 addresses, enable IPv6 routing, and verify connectivity between these environments.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**

1. Use Cisco Packet Tracer or GNS3.
2. Add a router and connect PCs representing each environment.
3. Ensure the router supports IPv6.

---

### **Step 2: Enable IPv6 on the Router**

1. Access the router CLI.
2. Enable IPv6 routing:
   ```bash
   Router(config)# ipv6 unicast-routing
   ```

---

### **Step 3: Configure IPv6 Addresses**

1. Assign IPv6 addresses to router interfaces:
   - **Dev (GigabitEthernet0/0):**
     ```bash
     Router(config)# interface gigabitEthernet 0/0
     Router(config-if)# ipv6 address 2001:db8:10::1/64
     Router(config-if)# no shutdown
     ```
   - **QA (GigabitEthernet0/1):**
     ```bash
     Router(config)# interface gigabitEthernet 0/1
     Router(config-if)# ipv6 address 2001:db8:20::1/64
     Router(config-if)# no shutdown
     ```
   - **Stage (GigabitEthernet0/2):**
     ```bash
     Router(config)# interface gigabitEthernet 0/2
     Router(config-if)# ipv6 address 2001:db8:30::1/64
     Router(config-if)# no shutdown
     ```
   - **Production (GigabitEthernet0/3):**
     ```bash
     Router(config)# interface gigabitEthernet 0/3
     Router(config-if)# ipv6 address 2001:db8:40::1/64
     Router(config-if)# no shutdown
     ```

2. Configure IPv6 addresses on PCs:
   - **PC 1 (Dev):**
     ```bash
     IP Address: 2001:db8:10::2
     Subnet Mask: 64
     Default Gateway: 2001:db8:10::1
     ```

   - Repeat for PCs in QA, Stage, and Production.

---

### **Step 4: Configure IPv6 Routing**

1. Set up static routes:
   - On Router A:
     ```bash
     Router(config)# ipv6 route 2001:db8:20::/64 gigabitEthernet 0/1
     Router(config)# ipv6 route 2001:db8:30::/64 gigabitEthernet 0/2
     Router(config)# ipv6 route 2001:db8:40::/64 gigabitEthernet 0/3
     ```

2. Alternatively, enable dynamic routing using OSPFv3:
   - Enable OSPFv3 globally:
     ```bash
     Router(config)# ipv6 router ospf 1
     ```
   - Assign interfaces to OSPFv3:
     ```bash
     Router(config)# interface gigabitEthernet 0/0
     Router(config-if)# ipv6 ospf 1 area 0
     Router(config)# interface gigabitEthernet 0/1
     Router(config-if)# ipv6 ospf 1 area 0
     Router(config)# interface gigabitEthernet 0/2
     Router(config-if)# ipv6 ospf 1 area 0
     Router(config)# interface gigabitEthernet 0/3
     Router(config-if)# ipv6 ospf 1 area 0
     ```

---

### **Step 5: Test IPv6 Connectivity**

1. From **PC 1 (Dev)**, ping the default gateway:
   ```bash
   ping 2001:db8:10::1
   ```

2. From **PC 2 (QA)**, ping **PC 3 (Stage)**:
   ```bash
   ping 2001:db8:30::2
   ```

3. Use traceroute to verify IPv6 path:
   ```bash
   traceroute6 2001:db8:40::2   # Linux/Mac
   tracert -6 2001:db8:40::2    # Windows
   ```

---

### **Step 6: Troubleshoot IPv6 Issues**

1. Verify IPv6 routing table:
   ```bash
   Router# show ipv6 route
   ```

2. Check interface status:
   ```bash
   Router# show ipv6 interface brief
   ```

3. Use Wireshark to analyze IPv6 traffic:
   - Filter for ICMPv6 packets:
     ```bash
     icmpv6
     ```

---

## **Hands-On Challenges**

1. **Simulate IPv6 Failures:**
   - Disable an interface and observe connectivity loss.

2. **Configure IPv6 Access Control:**
   - Create an ACL to block SSH traffic:
     ```bash
     Router(config)# ipv6 access-list BLOCK-SSH
     Router(config-ipv6-acl)# deny tcp any any eq 22
     Router(config-ipv6-acl)# permit ipv6 any any
     Router(config)# interface gigabitEthernet 0/0
     Router(config-if)# ipv6 traffic-filter BLOCK-SSH in
     ```

3. **Enable SLAAC (Stateless Address Autoconfiguration):**
   - Configure RA (Router Advertisement) for automatic IPv6 addressing:
     ```bash
     Router(config-if)# ipv6 nd prefix 2001:db8:10::/64
     ```

---

## **Expected Outcome**
- IPv6 addresses and routing are configured and verified.
- Connectivity between environments is tested successfully.
- Advanced IPv6 configurations like SLAAC and ACLs are implemented.

---

## **Key Takeaways**
- IPv6 addresses are scalable and eliminate the limitations of IPv4.
- Dynamic routing protocols like OSPFv3 simplify IPv6 network management.
- IPv6 configurations ensure readiness for modern network environments.

---
