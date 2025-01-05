# Lab 5: Troubleshooting Network Issues with Wireshark and Traceroute

## **Objective**
- Learn to troubleshoot connectivity issues between environments using Wireshark and Traceroute.
- Diagnose problems such as high latency, packet loss, and misconfigured devices.
- Apply troubleshooting techniques across **Dev**, **QA**, **Stage**, and **Production** environments.

---

## **Scenario**
Your organization is experiencing intermittent connectivity issues between the following environments:
1. **Dev (192.168.10.0/24)** and **QA (192.168.20.0/24)**: High latency.
2. **Stage (192.168.30.0/24)** and **Production (192.168.40.0/24)**: Packet loss.

You need to identify and resolve these issues using Wireshark and Traceroute.

---

## **Step-by-Step Instructions**

### **Step 1: Simulate the Network**
1. Use the **Lab 4 topology** (EIGRP setup).
2. Ensure all PCs and routers are configured as per the previous labs.

---

### **Step 2: Diagnose High Latency (Dev ↔ QA)**

#### **Wireshark Analysis**
1. Open Wireshark on **PC 1 (Dev)**.
2. Start capturing packets on the active interface.
3. From **PC 1 (Dev)**, ping **PC 2 (QA)**:
   ```bash
   ping 192.168.20.2
   ```
4. Stop the capture after the ping completes.
5. Filter ICMP packets in Wireshark:
   ```bash
   icmp
   ```
6. Analyze the **Round-Trip Time (RTT)** for each packet.

#### **Traceroute Analysis**
1. On **PC 1 (Dev)**, run Traceroute to **PC 2 (QA)**:
   ```bash
   tracert 192.168.20.2   # Windows
   traceroute 192.168.20.2 # Linux/Mac
   ```
2. Observe the latency at each hop.
3. Example output:
   ```
   Tracing route to 192.168.20.2
   1  <1 ms   <1 ms   <1 ms  192.168.10.1
   2   40 ms   50 ms   60 ms  192.168.20.1
   ```
4. Identify the hop causing high latency (e.g., Router A).

---

### **Step 3: Diagnose Packet Loss (Stage ↔ Production)**

#### **Wireshark Analysis**
1. Open Wireshark on **PC 3 (Stage)**.
2. Start capturing packets on the active interface.
3. From **PC 3 (Stage)**, initiate a continuous ping to **PC 4 (Production)**:
   ```bash
   ping 192.168.40.2 -t   # Windows
   ping 192.168.40.2      # Linux/Mac
   ```
4. Stop the capture after observing packet loss.
5. Filter ICMP packets in Wireshark:
   ```bash
   icmp
   ```
6. Look for retransmissions and errors in the ICMP reply packets.

#### **Traceroute Analysis**
1. On **PC 3 (Stage)**, run Traceroute to **PC 4 (Production)**:
   ```bash
   tracert 192.168.40.2   # Windows
   traceroute 192.168.40.2 # Linux/Mac
   ```
2. Observe the hop where packets are dropped.

---

### **Step 4: Resolve the Issues**

#### **High Latency (Dev ↔ QA):**
1. Check the configuration of Router A:
   ```bash
   RouterA# show ip route
   ```
2. Optimize EIGRP metrics on Router A:
   ```bash
   RouterA(config)# interface gig0/1
   RouterA(config-if)# delay 100
   RouterA(config-if)# bandwidth 1000
   ```
3. Verify improved latency using ping and Traceroute.

#### **Packet Loss (Stage ↔ Production):**
1. Check for interface errors on Router C:
   ```bash
   RouterC# show interfaces gig0/1
   ```
2. Clear interface statistics:
   ```bash
   RouterC# clear counters gig0/1
   ```
3. Replace faulty cable in Packet Tracer or simulate a clean connection.
4. Test connectivity using continuous ping.

---

## **Example Outputs**

### **Wireshark Output for Packet Loss**
```
Frame 1: 74 bytes on wire (592 bits)
Internet Protocol Version 4
    Source: 192.168.30.2
    Destination: 192.168.40.2
Internet Control Message Protocol
    Type: 8 (Echo)
    Code: 0
    Response: No reply received
```

### **Traceroute Output**
```
Tracing route to 192.168.40.2
1   <1 ms   <1 ms   <1 ms  192.168.30.1
2   *       *       *      Request timed out
```

---

## **Hands-On Challenges**
1. **Advanced Wireshark Filters:**
   - Filter by source IP and destination port:
     ```bash
     ip.src == 192.168.10.2 && tcp.port == 80
     ```
2. **Simulate Routing Loops:**
   - Introduce a misconfigured static route in Router B.
   - Observe the looping packets in Wireshark and resolve the issue.

3. **Compare Traceroute and Wireshark:**
   - Use Traceroute for path discovery and Wireshark for detailed packet analysis.

---

## **Expected Outcome**
- You’ll identify and resolve high latency and packet loss issues.
- You’ll gain expertise in using Wireshark and Traceroute for troubleshooting.
- You’ll understand how routing and network errors affect data flow.

---

## **Key Takeaways**
- Wireshark excels at detailed packet analysis, while Traceroute is great for path discovery.
- Troubleshooting tools are essential for diagnosing and resolving network issues.
- Simulating real-world scenarios prepares you for practical networking challenges.

---
