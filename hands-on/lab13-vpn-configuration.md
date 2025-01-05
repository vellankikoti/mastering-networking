# Lab 13: Configuring VPNs (Virtual Private Networks)

## **Objective**
- Understand VPN concepts and their role in secure communication.
- Configure a site-to-site IPsec VPN between two routers.
- Verify encrypted communication between Dev, QA, Stage, and Production environments.

---

## **Scenario**
Your organization operates across two remote sites:
1. **Site A:** Hosts Dev (192.168.10.0/24) and QA (192.168.20.0/24).
2. **Site B:** Hosts Stage (192.168.30.0/24) and Production (192.168.40.0/24).

You need to configure a site-to-site IPsec VPN between the routers at Site A and Site B to secure communication.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add two routers:
   - **Router A (Site A):** Connects Dev and QA.
   - **Router B (Site B):** Connects Stage and Production.
3. Connect the two routers through a simulated internet.

---

### **Step 2: Configure IPsec VPN on Site A Router**

#### **1. Configure IKE Phase 1:**
1. Define the ISAKMP policy:
   ```bash
   RouterA(config)# crypto isakmp policy 1
   RouterA(config-isakmp)# encryption aes
   RouterA(config-isakmp)# hash sha256
   RouterA(config-isakmp)# authentication pre-share
   RouterA(config-isakmp)# group 14
   RouterA(config-isakmp)# lifetime 86400
   ```
2. Set the pre-shared key:
   ```bash
   RouterA(config)# crypto isakmp key vpnpassword address 203.0.113.2
   ```

#### **2. Configure IKE Phase 2 (IPsec):**
1. Define the transform set:
   ```bash
   RouterA(config)# crypto ipsec transform-set VPN-TRANSFORM esp-aes esp-sha-hmac
   ```
2. Create the crypto map:
   ```bash
   RouterA(config)# crypto map VPN-MAP 10 ipsec-isakmp
   RouterA(config-crypto-map)# set peer 203.0.113.2
   RouterA(config-crypto-map)# set transform-set VPN-TRANSFORM
   RouterA(config-crypto-map)# match address VPN-ACL
   ```

#### **3. Define Interesting Traffic:**
1. Create an access list to define traffic to be encrypted:
   ```bash
   RouterA(config)# access-list 100 permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
   RouterA(config)# access-list 100 permit ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255
   ```

#### **4. Apply Crypto Map to the Interface:**
1. Apply the crypto map to the public interface:
   ```bash
   RouterA(config)# interface gigabitEthernet 0/0
   RouterA(config-if)# crypto map VPN-MAP
   ```

---

### **Step 3: Configure IPsec VPN on Site B Router**
1. Repeat the same steps on **Router B**, but adjust the IP addresses:
   - Set the peer address to **203.0.113.1**.
   - Define the reverse access list:
     ```bash
     RouterB(config)# access-list 100 permit ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
     RouterB(config)# access-list 100 permit ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
     ```

---

### **Step 4: Verify VPN Configuration**
1. Check the IPsec Security Associations (SAs):
   ```bash
   RouterA# show crypto ipsec sa
   ```
   Example output:
   ```
   Interface: GigabitEthernet0/0
   Crypto Map: VPN-MAP
   Peer: 203.0.113.2
     Security association: active
     Transform: esp-aes esp-sha-hmac
     Packets encapsulated: 100
     Packets decapsulated: 100
   ```

2. Verify IKE Phase 1:
   ```bash
   RouterA# show crypto isakmp sa
   ```
   Example output:
   ```
   State: MM_ACTIVE
   ```

---

### **Step 5: Test Encrypted Communication**
1. From **PC 1 (Dev)**, ping **PC 3 (Stage)**:
   ```bash
   ping 192.168.30.2
   ```
   - Expected output:
     ```
     Reply from 192.168.30.2: bytes=32 time<1ms TTL=64
     ```

2. From **PC 2 (QA)**, ping **PC 4 (Production)**:
   ```bash
   ping 192.168.40.2
   ```

3. Use Wireshark on the public interface of Router A to capture encrypted packets. Filter ESP traffic:
   ```bash
   esp
   ```

---

### **Hands-On Challenges**
1. **Simulate VPN Failures:**
   - Disable the crypto map on Router B and observe connectivity loss:
     ```bash
     RouterB(config)# interface gigabitEthernet 0/0
     RouterB(config-if)# no crypto map VPN-MAP
     ```

2. **Configure Split Tunneling:**
   - Allow only specific traffic through the VPN while permitting direct internet access.

3. **Advanced Encryption:**
   - Use AES-256 and SHA-512 for stronger encryption:
     ```bash
     RouterA(config-isakmp)# encryption aes 256
     RouterA(config-isakmp)# hash sha512
     ```

---

## **Expected Outcome**
- IPsec VPN is established between Site A and Site B.
- Traffic between private networks is encrypted and secured.
- Failover and advanced configurations are tested successfully.

---

## **Key Takeaways**
- IPsec VPNs ensure secure communication between remote sites.
- IKE Phase 1 and Phase 2 handle secure key exchange and encryption.
- Hands-on experience with VPNs prepares you for real-world implementations.

---
