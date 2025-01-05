# Lab 17: Network Security with Firewalls and IDS

## **Objective**
- Configure firewalls to allow or block traffic based on policies.
- Set up an intrusion detection system (IDS) to detect and monitor threats.
- Test firewall and IDS functionality across environments.

---

## **Scenario**
Your organization needs to secure the following environments:
1. **Dev (192.168.10.0/24)**: Allow outbound HTTP/HTTPS traffic only.
2. **QA (192.168.20.0/24)**: Block access to the Production environment.
3. **Stage (192.168.30.0/24)**: Detect unauthorized SSH login attempts.
4. **Production (192.168.40.0/24)**: Allow only critical application traffic (e.g., TCP port 5000).

You need to configure a firewall and IDS to enforce these rules and monitor for security breaches.

---

## **Step-by-Step Instructions**

### **Part 1: Configuring Firewalls**

#### **1. Configure Firewall Rules on a Router**
1. Access the router CLI.
2. Create ACLs for traffic filtering:
   - **Allow HTTP/HTTPS for Dev:**
     ```bash
     Router(config)# access-list 100 permit tcp 192.168.10.0 0.0.0.255 any eq 80
     Router(config)# access-list 100 permit tcp 192.168.10.0 0.0.0.255 any eq 443
     Router(config)# access-list 100 deny ip any any
     ```
   - Apply the ACL to the Dev interface:
     ```bash
     Router(config)# interface gigabitEthernet 0/1
     Router(config-if)# ip access-group 100 out
     ```

   - **Block QA Access to Production:**
     ```bash
     Router(config)# access-list 101 deny ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255
     Router(config)# access-list 101 permit ip any any
     ```
     - Apply the ACL to the QA interface:
       ```bash
       Router(config)# interface gigabitEthernet 0/2
       Router(config-if)# ip access-group 101 out
       ```

3. Verify ACL functionality:
   ```bash
   Router# show access-lists
   ```

---

### **Part 2: Setting Up an IDS**

#### **1. Install Snort**
1. Install Snort on a Linux system:
   ```bash
   sudo apt update
   sudo apt install snort
   ```

2. Configure Snort to monitor the Stage environment interface:
   - Edit the Snort configuration file (`/etc/snort/snort.conf`) to include:
     ```bash
     ipvar HOME_NET 192.168.30.0/24
     ```
   - Start Snort in IDS mode:
     ```bash
     sudo snort -i eth0 -A console -c /etc/snort/snort.conf
     ```

---

#### **2. Create Custom Rules**
1. Add a rule to detect SSH brute-force attempts:
   - Open the Snort rules file (`/etc/snort/rules/local.rules`) and add:
     ```
     alert tcp any any -> 192.168.30.0/24 22 (msg:"SSH brute-force attempt detected"; flags:S; threshold:type threshold,track by_src,count 5,seconds 60; sid:1000001;)
     ```
2. Restart Snort to apply the new rule:
   ```bash
   sudo systemctl restart snort
   ```

---

### **Step 3: Test Firewall and IDS Functionality**

1. **Test Firewall Rules:**
   - From **PC 1 (Dev)**, access a website via HTTP/HTTPS:
     ```bash
     curl http://example.com
     curl https://example.com
     ```
     - Ensure other traffic is blocked.

   - From **PC 2 (QA)**, ping **PC 4 (Production)**:
     ```bash
     ping 192.168.40.2
     ```
     - Ensure the connection is blocked.

2. **Test IDS Rules:**
   - Simulate an SSH brute-force attack on **PC 3 (Stage)**:
     ```bash
     for i in {1..6}; do ssh user@192.168.30.2; done
     ```
   - Check Snort console output for alerts:
     ```
     [**] [1:1000001:0] SSH brute-force attempt detected [**]
     ```

---

### **Hands-On Challenges**

1. **Enhance Firewall Rules:**
   - Add rules to limit bandwidth usage for Dev and QA environments.

2. **Simulate Attacks:**
   - Simulate a DDoS attack using a tool like `hping3` and observe IDS alerts.

3. **Advanced IDS Rules:**
   - Write Snort rules to detect:
     - Unauthorized file downloads.
     - SQL injection attempts.

4. **Monitor Traffic with Wireshark:**
   - Capture traffic between QA and Production to verify blocked packets.

---

## **Expected Outcome**
- Firewall rules successfully filter traffic as per the defined policies.
- The IDS detects and logs potential threats like SSH brute-force attempts.
- Traffic monitoring confirms security policies are enforced.

---

## **Key Takeaways**
- Firewalls control traffic based on policies, ensuring secure communication.
- IDS monitors network activity to detect and respond to threats.
- Combining firewalls and IDS provides comprehensive network security.

---
