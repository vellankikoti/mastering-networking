# Lab 18: Network Performance Monitoring and Troubleshooting

## **Objective**
- Use network monitoring tools to measure performance metrics.
- Diagnose and resolve network bottlenecks and issues.
- Test optimization strategies for improved network performance.

---

## **Scenario**
Your organization is experiencing the following performance issues:
1. High latency in the Dev environment.
2. Bandwidth congestion in the QA environment.
3. Packet loss in the Stage environment.
4. Performance degradation in the Production environment due to excessive broadcast traffic.

You will use monitoring tools to diagnose and resolve these issues.

---

## **Step-by-Step Instructions**

### **Step 1: Monitor Network Performance**

#### **1. Use Ping and Traceroute for Latency Analysis**
1. From **PC 1 (Dev)**, run a ping test to the gateway:
   ```bash
   ping 192.168.10.1
   ```
   - Check round-trip time (RTT) for high latency.
2. Run a traceroute to identify latency at each hop:
   ```bash
   traceroute 192.168.20.2   # Linux/Mac
   tracert 192.168.20.2      # Windows
   ```

#### **2. Use Bandwidth Monitoring Tools**
1. Install `iperf` on two systems:
   ```bash
   sudo apt install iperf
   ```
2. Start an `iperf` server on **PC 2 (QA)**:
   ```bash
   iperf -s
   ```
3. Run an `iperf` client from **PC 1 (Dev)** to measure bandwidth:
   ```bash
   iperf -c 192.168.20.2
   ```

#### **3. Use Wireshark for Packet Analysis**
1. Start Wireshark on **PC 3 (Stage)** and capture traffic.
2. Filter for dropped or retransmitted packets:
   ```bash
   tcp.analysis.retransmission
   ```

#### **4. Use SNMP for Performance Monitoring**
1. Enable SNMP on the router:
   ```bash
   Router(config)# snmp-server community public RO
   Router(config)# snmp-server location DataCenter
   ```
2. Use an SNMP monitoring tool like **Cacti** or **PRTG** to visualize network performance.

---

### **Step 2: Diagnose and Resolve Issues**

#### **1. Resolve High Latency in Dev**
- Identify overloaded links or interfaces using:
  ```bash
  Router# show interfaces
  ```
- Increase bandwidth allocation for the affected interface:
  ```bash
  Router(config)# interface gigabitEthernet 0/0
  Router(config-if)# bandwidth 100000
  ```

#### **2. Resolve Bandwidth Congestion in QA**
- Apply traffic shaping using QoS:
  ```bash
  Router(config)# policy-map SHAPE-QA
  Router(config-pmap)# class class-default
  Router(config-pmap-c)# shape average 1000000
  Router(config)# interface gigabitEthernet 0/1
  Router(config-if)# service-policy output SHAPE-QA
  ```

#### **3. Resolve Packet Loss in Stage**
- Identify and replace faulty cables or ports using:
  ```bash
  Router# show interfaces
  ```
- Adjust MTU to avoid fragmentation:
  ```bash
  Router(config)# interface gigabitEthernet 0/2
  Router(config-if)# mtu 1400
  ```

#### **4. Resolve Broadcast Traffic in Production**
- Enable storm control on the switch interface:
  ```bash
  Switch(config)# interface fastEthernet 0/1
  Switch(config-if)# storm-control broadcast level 10.00
  ```

---

### **Step 3: Test and Verify Optimizations**

1. Re-run ping and traceroute tests in the Dev environment to verify latency improvement.
2. Use `iperf` to measure bandwidth in the QA environment after applying traffic shaping.
3. Monitor packet retransmissions in Wireshark for the Stage environment.
4. Observe reduced broadcast traffic in the Production environment using:
   ```bash
   Switch# show storm-control
   ```

---

## **Hands-On Challenges**

1. **Simulate High Latency:**
   - Introduce artificial delay using `tc` in Linux:
     ```bash
     sudo tc qdisc add dev eth0 root netem delay 100ms
     ```

2. **Analyze and Reduce Jitter:**
   - Use `iperf` in UDP mode to measure jitter:
     ```bash
     iperf -c 192.168.20.2 -u -b 1M
     ```

3. **Capture Network Utilization:**
   - Use tools like `nload` or `iftop` to monitor real-time network utilization.

---

## **Expected Outcome**
- Performance metrics like latency, bandwidth, and packet loss are measured and optimized.
- Bottlenecks and issues are resolved, ensuring efficient network operation.
- Monitoring tools validate improvements across environments.

---

## **Key Takeaways**
- Network monitoring tools like Wireshark, SNMP, and `iperf` are invaluable for diagnosing performance issues.
- Techniques like QoS, traffic shaping, and storm control help optimize network performance.
- Hands-on troubleshooting ensures readiness for real-world network challenges.

---
