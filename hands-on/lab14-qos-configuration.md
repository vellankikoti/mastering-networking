# Lab 14: Configuring QoS (Quality of Service)

## **Objective**
- Understand how QoS prioritizes network traffic.
- Configure traffic classes and policies to prioritize critical traffic.
- Test and verify QoS implementation across Dev, QA, Stage, and Production environments.

---

## **Scenario**
Your organization has the following traffic priorities:
1. **Dev Environment (192.168.10.0/24):** Best-effort traffic.
2. **QA Environment (192.168.20.0/24):** Medium priority for file transfers.
3. **Stage Environment (192.168.30.0/24):** High priority for VoIP traffic.
4. **Production Environment (192.168.40.0/24):** Critical priority for application data.

You need to configure QoS to ensure that critical and high-priority traffic receives the highest bandwidth and lowest latency.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use Cisco Packet Tracer or GNS3.
2. Add a router and connect it to PCs representing each environment.

---

### **Step 2: Define QoS Traffic Classes**

1. Access the router CLI.
2. Create class maps to classify traffic:
   - **Classify VoIP Traffic (Stage):**
     ```bash
     Router(config)# class-map match-any HIGH-PRIORITY
     Router(config-cmap)# match protocol rtp
     Router(config-cmap)# exit
     ```

   - **Classify File Transfer Traffic (QA):**
     ```bash
     Router(config)# class-map match-any MEDIUM-PRIORITY
     Router(config-cmap)# match protocol ftp
     Router(config-cmap)# exit
     ```

   - **Default Class for Dev:**
     No configuration is needed for default best-effort traffic.

---

### **Step 3: Define QoS Policies**

1. Create a policy map to define actions for each traffic class:
   - **Set Bandwidth and Priority:**
     ```bash
     Router(config)# policy-map QoS-POLICY
     Router(config-pmap)# class HIGH-PRIORITY
     Router(config-pmap-c)# priority percent 30
     Router(config-pmap-c)# exit
     Router(config-pmap)# class MEDIUM-PRIORITY
     Router(config-pmap-c)# bandwidth percent 20
     Router(config-pmap-c)# exit
     Router(config-pmap)# class class-default
     Router(config-pmap-c)# fair-queue
     Router(config-pmap-c)# exit
     ```

---

### **Step 4: Apply QoS to Interfaces**

1. Apply the QoS policy to the outgoing interface:
   ```bash
   Router(config)# interface gigabitEthernet 0/0
   Router(config-if)# service-policy output QoS-POLICY
   ```

---

### **Step 5: Verify QoS Configuration**

1. Check the active QoS policy:
   ```bash
   Router# show policy-map interface gigabitEthernet 0/0
   ```
   Example output:
   ```
   GigabitEthernet0/0
     Service-policy output: QoS-POLICY
       Class-map: HIGH-PRIORITY
         Priority: 30% of bandwidth
       Class-map: MEDIUM-PRIORITY
         Bandwidth: 20% of bandwidth
       Class-map: class-default
         Fair queue
   ```

2. Simulate high-priority traffic:
   - From **PC 3 (Stage)**, start an RTP stream:
     ```bash
     rtp-stream --source 192.168.30.2 --destination 192.168.40.2
     ```
   - Monitor bandwidth allocation using:
     ```bash
     Router# show policy-map interface gigabitEthernet 0/0
     ```

---

### **Step 6: Test QoS Functionality**

1. Generate competing traffic:
   - Start file transfers from **PC 2 (QA)** to **PC 4 (Production)**:
     ```bash
     ftp 192.168.40.2
     ```
   - Observe that VoIP traffic (RTP) from Stage maintains priority.

2. Use Wireshark to capture and analyze traffic prioritization:
   - Filter RTP packets:
     ```bash
     udp.port == 5004
     ```

---

### **Hands-On Challenges**

1. **Simulate Network Congestion:**
   - Generate high traffic load from all environments and observe how QoS prioritizes critical traffic.

2. **Advanced QoS Features:**
   - Configure a hierarchical QoS policy to combine shaping and prioritization:
     ```bash
     Router(config)# policy-map PARENT-POLICY
     Router(config-pmap)# class class-default
     Router(config-pmap-c)# shape average 10000000
     Router(config-pmap-c)# service-policy QoS-POLICY
     ```

3. **Modify Traffic Priorities:**
   - Change bandwidth allocation dynamically and test its impact.

---

## **Expected Outcome**
- Critical and high-priority traffic (Stage and Production) receives the highest bandwidth.
- Medium-priority traffic (QA) is allocated sufficient bandwidth without starving other classes.
- Best-effort traffic (Dev) is served with fair queueing.

---

## **Key Takeaways**
- QoS ensures efficient use of bandwidth by prioritizing critical traffic.
- Traffic classification and policies provide granular control.
- Hands-on experience with QoS prepares you for managing enterprise networks.

---
