# Lab 15: Network Automation with Python and Ansible

## **Objective**
- Automate repetitive network configuration tasks using Python scripts.
- Leverage Ansible to manage multiple network devices simultaneously.
- Build reusable playbooks and scripts for various network scenarios.

---

## **Scenario**
Your organization needs to automate the following tasks across Dev, QA, Stage, and Production environments:
1. Configure VLANs and interfaces on multiple switches.
2. Apply QoS policies on multiple routers.
3. Collect interface status and routing table information periodically.

You will use Python and Ansible to accomplish these tasks.

---

## **Step-by-Step Instructions**

### **Part 1: Automating with Python**

#### **1. Automate VLAN Configuration**
1. Install the required Python library:
   ```bash
   pip install netmiko
   ```
2. Create a Python script (`configure_vlan.py`):
   ```python
   from netmiko import ConnectHandler

   # Device details
   switch = {
       'device_type': 'cisco_ios',
       'host': '192.168.10.1',
       'username': 'admin',
       'password': 'password',
   }

   # Commands to configure VLANs
   commands = [
       'vlan 10',
       'name Dev',
       'vlan 20',
       'name QA',
       'vlan 30',
       'name Stage',
       'vlan 40',
       'name Production'
   ]

   # Connect and send commands
   net_connect = ConnectHandler(**switch)
   output = net_connect.send_config_set(commands)
   print(output)
   net_connect.disconnect()
   ```

3. Run the script:
   ```bash
   python configure_vlan.py
   ```

---

#### **2. Automate Interface Monitoring**
1. Create a Python script (`monitor_interfaces.py`):
   ```python
   from netmiko import ConnectHandler

   # Device details
   router = {
       'device_type': 'cisco_ios',
       'host': '192.168.10.1',
       'username': 'admin',
       'password': 'password',
   }

   # Connect and retrieve interface status
   net_connect = ConnectHandler(**router)
   output = net_connect.send_command('show ip interface brief')
   print(output)
   net_connect.disconnect()
   ```

2. Run the script:
   ```bash
   python monitor_interfaces.py
   ```

---

### **Part 2: Automating with Ansible**

#### **1. Install Ansible**
1. Install Ansible on your system:
   ```bash
   sudo apt install ansible
   ```

#### **2. Configure Inventory File**
1. Create an inventory file (`hosts`):
   ```ini
   [routers]
   router1 ansible_host=192.168.10.1 ansible_user=admin ansible_password=password
   router2 ansible_host=192.168.20.1 ansible_user=admin ansible_password=password

   [switches]
   switch1 ansible_host=192.168.30.1 ansible_user=admin ansible_password=password
   switch2 ansible_host=192.168.40.1 ansible_user=admin ansible_password=password
   ```

---

#### **3. Create an Ansible Playbook**
1. Create a playbook (`configure_qos.yml`) to apply QoS policies:
   ```yaml
   - name: Configure QoS on routers
     hosts: routers
     gather_facts: no
     tasks:
       - name: Apply QoS policy
         ios_config:
           lines:
             - class-map match-any HIGH-PRIORITY
             - match protocol rtp
             - policy-map QoS-POLICY
             - class HIGH-PRIORITY
             - priority percent 30
           parents: []
   ```

2. Run the playbook:
   ```bash
   ansible-playbook -i hosts configure_qos.yml
   ```

---

#### **4. Collect Interface and Routing Data**
1. Create a playbook (`collect_data.yml`):
   ```yaml
   - name: Collect interface and routing data
     hosts: routers
     gather_facts: no
     tasks:
       - name: Gather interface status
         ios_command:
           commands:
             - show ip interface brief
       - name: Gather routing table
         ios_command:
           commands:
             - show ip route
   ```

2. Run the playbook:
   ```bash
   ansible-playbook -i hosts collect_data.yml
   ```

---

### **Hands-On Challenges**

1. **Dynamic VLAN Assignment:**
   - Modify the Python script to dynamically assign VLANs based on user input.

2. **Periodic Data Collection:**
   - Schedule the Ansible playbook to run every hour using `cron` or `systemd`.

3. **Multi-Device Automation:**
   - Add more devices to the inventory file and apply configurations across all devices simultaneously.

---

## **Expected Outcome**
- Python automates repetitive tasks such as VLAN configuration and interface monitoring.
- Ansible manages multiple devices efficiently, applying configurations and collecting data.
- Automation reduces manual effort and minimizes errors.

---

## **Key Takeaways**
- Python is ideal for scripting specific tasks on individual devices.
- Ansible excels at managing multiple devices simultaneously with reusable playbooks.
- Network automation ensures scalability, consistency, and efficiency in large-scale environments.

---
