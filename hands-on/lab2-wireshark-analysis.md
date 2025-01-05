# Lab 2: Traffic Analysis with Wireshark Across Environments

## **Objective**
- Capture and analyze network traffic using Wireshark.
- Understand how data flows between **Dev**, **QA**, **Stage**, and **Production** environments.
- Learn to filter and decode packets to troubleshoot issues.

---

## **Scenario**
You are tasked with monitoring and analyzing network traffic between the **Dev** and **QA** environments. Additionally, you need to ensure that communication between **Stage** and **Production** is functioning without errors.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**
1. Use the **Lab 1 setup** with the following networks:
   - **Dev:** 192.168.10.0/24
   - **QA:** 192.168.20.0/24
   - **Stage:** 192.168.30.0/24
   - **Production:** 192.168.40.0/24
2. Ensure the router is configured, and PCs are assigned IP addresses as detailed in Lab 1.

---

### **Step 2: Install and Open Wireshark**
1. Install Wireshark on **PC 1 (Dev)** and **PC 3 (Stage)**.
2. Open Wireshark and select the active network interface.

---

### **Step 3: Capture Traffic**
1. Start capturing packets on PC 1 (Dev).
2. From PC 1 (Dev), ping PC 2 (QA):
   ```bash
   ping 192.168.20.2
   ```
3. Stop the Wireshark capture after the ping command completes.

---

### **Step 4: Analyze the Captured Traffic**
1. Filter the captured traffic for ICMP packets:
   ```bash
   icmp
   ```
2. Select a packet in the capture window to analyze its layers:
   - **Ethernet Layer:** Source and destination MAC addresses.
   - **IP Layer:** Source and destination IP addresses.
   - **ICMP Layer:** Echo request and reply details.

---

### **Step 5: Analyze Traffic Between Stage and Production**
1. Start capturing packets on PC 3 (Stage).
2. From PC 3 (Stage), initiate a file transfer to PC 4 (Production) using a tool like `scp`:
   ```bash
   scp sample.txt user@192.168.40.2:/destination/path
   ```
3. Stop the capture after the transfer is complete.
4. Filter the captured traffic for TCP packets:
   ```bash
   tcp
   ```
5. Analyze the TCP three-way handshake:
   - SYN, SYN-ACK, ACK packets.
   - Port numbers and sequence numbers.

---

## **Example Output**
### **ICMP Packet Details**
```
Frame 1: 74 bytes on wire (592 bits), 74 bytes captured (592 bits)
Ethernet II
    Destination: 00:1a:2b:3c:4d:5e
    Source: 00:5e:6f:7a:8b:9c
Internet Protocol Version 4
    Source: 192.168.10.2
    Destination: 192.168.20.2
Internet Control Message Protocol
    Type: 8 (Echo)
    Code: 0
```

### **TCP Packet Details**
```
Frame 12: 66 bytes on wire (528 bits), 66 bytes captured (528 bits)
Ethernet II
    Destination: 00:1f:16:8d:35:7c
    Source: 00:0c:29:3e:6d:21
Internet Protocol Version 4
    Source: 192.168.30.2
    Destination: 192.168.40.2
Transmission Control Protocol
    Source Port: 1025
    Destination Port: 22
    [SYN] Sequence Number: 0
```

---

## **Hands-On Challenges**
1. **Simulate Network Issues:**
   - Disconnect a network cable between the router and one of the PCs.
   - Observe how traffic behaves in Wireshark and identify dropped packets.
2. **Advanced Filtering:**
   - Filter DNS traffic:
     ```bash
     dns
     ```
   - Filter traffic between specific IPs:
     ```bash
     ip.src == 192.168.10.2 && ip.dst == 192.168.20.2
     ```

---

## **Expected Outcome**
- You’ll understand how traffic flows between environments.
- You’ll analyze packets to troubleshoot potential issues.
- You’ll gain insights into ICMP, TCP, and other protocols.

---

## **Key Takeaways**
- Wireshark provides detailed visibility into network traffic.
- Using filters simplifies packet analysis for troubleshooting.
- Monitoring traffic between Dev, QA, Stage, and Production environments ensures operational consistency.

---

