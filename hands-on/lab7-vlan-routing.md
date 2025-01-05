# Lab 7: Configuring VLANs and Inter-VLAN Routing

## **Objective**
- Create VLANs to logically segment networks within an organization.
- Configure inter-VLAN routing to enable communication between VLANs.
- Simulate real-world environments (Dev, QA, Stage, Production) using VLANs.

---

## **Scenario**
Your organization uses a single switch to manage the following environments:
1. **Dev (VLAN 10):** 192.168.10.0/24
2. **QA (VLAN 20):** 192.168.20.0/24
3. **Stage (VLAN 30):** 192.168.30.0/24
4. **Production (VLAN 40):** 192.168.40.0/24

You need to configure VLANs on the switch and set up a router for inter-VLAN routing.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add the following devices:
   - 1 Layer 2 switch.
   - 1 Router.
   - 4 PCs (one for each environment).
3. Connect all PCs to the switch and the switch to the router using a trunk link.

---

### **Step 2: Configure VLANs on the Switch**
1. Access the switch CLI.
2. Enter global configuration mode:
   ```bash
   Switch> enable
   Switch# configure terminal
   ```
3. Create VLANs:
   ```bash
   Switch(config)# vlan 10
   Switch(config-vlan)# name Dev
   Switch(config-vlan)# exit
   Switch(config)# vlan 20
   Switch(config-vlan)# name QA
   Switch(config-vlan)# exit
   Switch(config)# vlan 30
   Switch(config-vlan)# name Stage
   Switch(config-vlan)# exit
   Switch(config)# vlan 40
   Switch(config-vlan)# name Production
   ```
4. Assign ports to VLANs:
   - **PC 1 (Dev):**
     ```bash
     Switch(config)# interface fastEthernet 0/1
     Switch(config-if)# switchport mode access
     Switch(config-if)# switchport access vlan 10
     Switch(config-if)# exit
     ```
   - Repeat for QA (VLAN 20), Stage (VLAN 30), and Production (VLAN 40).

5. Verify VLAN configuration:
   ```bash
   Switch# show vlan brief
   ```

---

### **Step 3: Configure the Trunk Link**
1. On the switch, configure the trunk link to the router:
   ```bash
   Switch(config)# interface fastEthernet 0/24
   Switch(config-if)# switchport mode trunk
   ```

---

### **Step 4: Configure the Router for Inter-VLAN Routing**
1. Access the router CLI.
2. Enter global configuration mode:
   ```bash
   Router> enable
   Router# configure terminal
   ```
3. Configure subinterfaces for each VLAN:
   - **VLAN 10 (Dev):**
     ```bash
     Router(config)# interface gigabitEthernet 0/0.10
     Router(config-subif)# encapsulation dot1Q 10
     Router(config-subif)# ip address 192.168.10.1 255.255.255.0
     Router(config-subif)# exit
     ```
   - **VLAN 20 (QA):**
     ```bash
     Router(config)# interface gigabitEthernet 0/0.20
     Router(config-subif)# encapsulation dot1Q 20
     Router(config-subif)# ip address 192.168.20.1 255.255.255.0
     Router(config-subif)# exit
     ```
   - Repeat for VLAN 30 (Stage) and VLAN 40 (Production).

4. Verify subinterface configuration:
   ```bash
   Router# show ip interface brief
   ```

---

### **Step 5: Test Inter-VLAN Communication**
1. Assign static IPs to the PCs:
   - **PC 1 (Dev):**
     - IP: `192.168.10.2`
     - Subnet Mask: `255.255.255.0`
     - Default Gateway: `192.168.10.1`
   - Repeat for QA, Stage, and Production PCs with respective IPs.
2. Test communication:
   - From **PC 1 (Dev)**, ping **PC 2 (QA)**:
     ```bash
     ping 192.168.20.2
     ```
   - From **PC 3 (Stage)**, ping **PC 4 (Production)**:
     ```bash
     ping 192.168.40.2
     ```
3. Expected output:
   ```
   Reply from 192.168.20.2: bytes=32 time<1ms TTL=64
   ```

---

## **Hands-On Challenges**
1. **Advanced VLANs:**
   - Create an additional VLAN for a testing environment (VLAN 50).
   - Configure subinterfaces and verify connectivity.
2. **Simulate Network Failure:**
   - Shut down the trunk link between the switch and router.
   - Observe communication failures and troubleshoot using:
     ```bash
     Switch# show interfaces trunk
     ```
3. **Analyze VLAN Traffic with Wireshark:**
   - Capture and analyze tagged VLAN traffic using:
     ```bash
     vlan.id == 10
     ```

---

## **Expected Outcome**
- VLANs segment traffic for Dev, QA, Stage, and Production environments.
- Inter-VLAN routing allows seamless communication between VLANs.
- You’ll understand how VLANs enhance security and efficiency in network design.

---

## **Key Takeaways**
- VLANs provide logical segmentation in Layer 2 networks.
- Inter-VLAN routing enables communication between isolated VLANs.
- Understanding VLANs is crucial for designing scalable and secure enterprise networks.

---
