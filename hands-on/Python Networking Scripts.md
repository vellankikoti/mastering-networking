# Python Networking Scripts

This document contains 20 essential Python networking scripts to master various aspects of networking. Each script includes clear objectives, purposes, expected outputs, and explanations to ensure maximum learning.

---

## **1. TCP Client-Server Communication**

**Objective:** Establish a basic TCP communication.

**Purpose:** Learn socket programming basics.

**Expected Output:** Server echoes messages sent by the client.

### **Server Code**
```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('0.0.0.0', 8080))
server.listen(5)
print("Server listening on port 8080...")

while True:
    client, addr = server.accept()
    print(f"Connection established from {addr}")
    data = client.recv(1024)
    print(f"Received: {data.decode()}")
    client.sendall(data)  # Echo message back
    client.close()
```

### **Client Code**
```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(('127.0.0.1', 8080))
message = "Hello, Server!"
client.sendall(message.encode())
response = client.recv(1024)
print(f"Server echoed: {response.decode()}")
client.close()
```

---

## **2. UDP Client-Server Communication**

**Objective:** Establish a basic UDP communication.

**Purpose:** Understand connectionless communication.

**Expected Output:** Server receives and echoes messages sent by the client.

### **Server Code**
```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server.bind(('0.0.0.0', 8080))
print("UDP Server listening on port 8080...")

while True:
    data, addr = server.recvfrom(1024)
    print(f"Received from {addr}: {data.decode()}")
    server.sendto(data, addr)  # Echo message back
```

### **Client Code**
```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_address = ('127.0.0.1', 8080)
message = "Hello, UDP Server!"
client.sendto(message.encode(), server_address)
response, _ = client.recvfrom(1024)
print(f"Server echoed: {response.decode()}")
client.close()
```

---

## **3. Simple Port Scanner**

**Objective:** Scan ports on a target host.

**Purpose:** Detect open ports on a target system.

**Expected Output:** List of open ports.

```python
import socket

def port_scanner(target, start_port, end_port):
    print(f"Scanning {target} from port {start_port} to {end_port}...")
    for port in range(start_port, end_port + 1):
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            s.settimeout(0.5)
            if s.connect_ex((target, port)) == 0:
                print(f"Port {port} is open")

target_ip = input("Enter target IP: ")
port_scanner(target_ip, 1, 100)
```

---

## **4. Ping Sweep for Network Discovery**

**Objective:** Discover active hosts in a network.

**Purpose:** Identify live devices in a subnet.

**Expected Output:** List of active hosts.

```python
import os
import platform
from subprocess import Popen, PIPE

def ping_sweep(network):
    for host in range(1, 255):
        ip = f"{network}.{host}"
        command = ["ping", "-c", "1", ip] if platform.system() != "Windows" else ["ping", "-n", "1", ip]
        process = Popen(command, stdout=PIPE, stderr=PIPE)
        process.communicate()
        if process.returncode == 0:
            print(f"{ip} is active")

network = input("Enter network (e.g., 192.168.1): ")
ping_sweep(network)
```

---

## **5. Capture Live Packets**

**Objective:** Capture and analyze network packets.

**Purpose:** Learn basic packet sniffing.

**Expected Output:** Summary of captured packets.

```python
from scapy.all import sniff

def packet_sniffer(packet):
    print(packet.summary())

print("Capturing 10 packets...")
sniff(prn=packet_sniffer, count=10)
```

---

## **6. HTTP Request Simulation**

**Objective:** Simulate an HTTP GET request using sockets.

**Purpose:** Understand low-level HTTP communication.

**Expected Output:** HTTP response from the server.

```python
import socket

def http_request(host):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, 80))
        s.sendall(b"GET / HTTP/1.1\r\nHost: " + host.encode() + b"\r\n\r\n")
        response = s.recv(4096)
        print(response.decode())

http_request("example.com")
```

---

## **7. Automate SSH Commands**

**Objective:** Automate SSH command execution.

**Purpose:** Manage devices programmatically.

**Expected Output:** Command output from the device.

```python
import paramiko

def execute_ssh_command(ip, username, password, command):
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(ip, username=username, password=password)
    stdin, stdout, stderr = ssh.exec_command(command)
    print(stdout.read().decode())
    ssh.close()

execute_ssh_command("192.168.1.1", "admin", "password", "show version")
```

---

## **8. Perform ARP Spoofing**

**Objective:** Simulate ARP spoofing.

**Purpose:** Understand ARP manipulation and its effects.

**Expected Output:** ARP spoof packets sent.

```python
from scapy.all import ARP, send

def arp_spoof(target_ip, gateway_ip):
    packet = ARP(op=2, pdst=target_ip, psrc=gateway_ip, hwdst="ff:ff:ff:ff:ff:ff")
    send(packet, loop=1, verbose=1)

arp_spoof("192.168.1.100", "192.168.1.1")
```

---

## **9. Measure Latency**

**Objective:** Measure latency using ICMP.

**Purpose:** Test network delay.

**Expected Output:** Average RTT.

```python
import subprocess

def measure_latency(host):
    result = subprocess.run(["ping", "-c", "4", host], stdout=subprocess.PIPE)
    print(result.stdout.decode())

measure_latency("google.com")
```

---

## **10. IP Geolocation Lookup**

**Objective:** Fetch geolocation details for an IP.

**Purpose:** Understand API integration for networking tasks.

**Expected Output:** Location details of the IP.

```python
import requests

def ip_geolocation(ip):
    url = f"https://ipapi.co/{ip}/json/"
    response = requests.get(url)
    data = response.json()
    print(f"IP: {data['ip']}, Location: {data['city']}, {data['country_name']}")

ip_geolocation("8.8.8.8")
```

---
### **11. Bandwidth Tester using `iperf`**

**Objective:** Measure network bandwidth between two devices.

**Purpose:** Test network performance in real-time.

**Expected Output:** Bandwidth report in Mbps or Gbps.

### **Code**

1. **Run the `iperf` server on one device:**
   ```bash
   iperf -s
   ```

2. **Run the client from another device:**
   ```bash
   iperf -c <server_ip>
   ```

---

### **12. Validate an IPv4 Address**

**Objective:** Validate if a string is a valid IPv4 address.

**Purpose:** Ensure correct IP address input for configurations.

**Expected Output:** True/False indicating if the address is valid.

### **Code**
```python
import re

def validate_ipv4(ip):
    pattern = r"^(25[0-5]|2[0-4][0-9]|[0-1]?[0-9][0-9]?)\." \
              r"(25[0-5]|2[0-4][0-9]|[0-1]?[0-9][0-9]?)\." \
              r"(25[0-5]|2[0-4][0-9]|[0-1]?[0-9][0-9]?)\." \
              r"(25[0-5]|2[0-4][0-9]|[0-1]?[0-9][0-9]?)$"
    return bool(re.match(pattern, ip))

ip_address = input("Enter an IP address: ")
print(f"Is valid IPv4: {validate_ipv4(ip_address)}")
```

---

### **13. Fetch Public IP**

**Objective:** Retrieve the public IP address of your device.

**Purpose:** Identify the outward-facing IP.

**Expected Output:** Public IP address.

### **Code**
```python
import requests

def fetch_public_ip():
    response = requests.get('https://api.ipify.org?format=json')
    data = response.json()
    return data['ip']

print(f"Your public IP is: {fetch_public_ip()}")
```

---

### **14. IPv6 Address Validator**

**Objective:** Validate if a string is a valid IPv6 address.

**Purpose:** Ensure proper IPv6 configurations.

**Expected Output:** True/False indicating if the address is valid.

### **Code**
```python
import ipaddress

def validate_ipv6(ip):
    try:
        ipaddress.IPv6Address(ip)
        return True
    except ipaddress.AddressValueError:
        return False

ipv6_address = input("Enter an IPv6 address: ")
print(f"Is valid IPv6: {validate_ipv6(ipv6_address)}")
```

---

### **15. DNS Resolver**

**Objective:** Resolve domain names to IP addresses.

**Purpose:** Test DNS configurations.

**Expected Output:** IP address(es) for a given domain.

### **Code**
```python
import socket

def resolve_domain(domain):
    try:
        ip = socket.gethostbyname(domain)
        print(f"The IP address of {domain} is {ip}")
    except socket.gaierror:
        print(f"Could not resolve {domain}")

domain = input("Enter a domain name: ")
resolve_domain(domain)
```

---

### **16. Retrieve Network Interface Information**

**Objective:** Display all active network interfaces and their IPs.

**Purpose:** Understand local network setup.

**Expected Output:** List of network interfaces and IP addresses.

### **Code**
```python
import netifaces

def list_interfaces():
    interfaces = netifaces.interfaces()
    for interface in interfaces:
        addrs = netifaces.ifaddresses(interface)
        if netifaces.AF_INET in addrs:
            ip_info = addrs[netifaces.AF_INET][0]
            print(f"Interface: {interface}, IP: {ip_info['addr']}")

list_interfaces()
```

---

### **17. Automate VLAN Configuration**

**Objective:** Configure VLANs on a switch using SSH.

**Purpose:** Automate repetitive switch configurations.

**Expected Output:** VLANs created on the switch.

### **Code**
```python
from netmiko import ConnectHandler

def configure_vlan(switch, vlans):
    connection = ConnectHandler(**switch)
    for vlan in vlans:
        commands = [f'vlan {vlan["id"]}', f'name {vlan["name"]}']
        output = connection.send_config_set(commands)
        print(output)
    connection.disconnect()

switch = {
    'device_type': 'cisco_ios',
    'host': '192.168.1.1',
    'username': 'admin',
    'password': 'password',
}

vlans = [{'id': 10, 'name': 'Dev'}, {'id': 20, 'name': 'QA'}]
configure_vlan(switch, vlans)
```

---

### **18. Create a Subnet Calculator**

**Objective:** Calculate subnet masks, network, and broadcast addresses.

**Purpose:** Simplify subnet calculations.

**Expected Output:** Subnet details for a given IP and CIDR.

### **Code**
```python
import ipaddress

def subnet_calculator(network):
    net = ipaddress.ip_network(network, strict=False)
    print(f"Network: {net.network_address}")
    print(f"Netmask: {net.netmask}")
    print(f"Broadcast: {net.broadcast_address}")
    print(f"Hosts: {list(net.hosts())}")

cidr = input("Enter a network (e.g., 192.168.1.0/24): ")
subnet_calculator(cidr)
```

---

### **19. Test HTTP Response Status**

**Objective:** Test the HTTP status code for a given URL.

**Purpose:** Check website availability.

**Expected Output:** HTTP status code.

### **Code**
```python
import requests

def test_http_status(url):
    try:
        response = requests.get(url)
        print(f"HTTP Status Code for {url}: {response.status_code}")
    except requests.exceptions.RequestException as e:
        print(f"Error: {e}")

url = input("Enter a URL: ")
test_http_status(url)
```

---

### **20. Generate Network Topology**

**Objective:** Generate a visual network topology from device connections.

**Purpose:** Visualize network structure.

**Expected Output:** Network topology graph.

### **Code**
```python
import networkx as nx
import matplotlib.pyplot as plt

def generate_topology(devices, connections):
    graph = nx.Graph()
    for device in devices:
        graph.add_node(device)
    for connection in connections:
        graph.add_edge(*connection)
    nx.draw(graph, with_labels=True, node_color='skyblue', node_size=1500)
    plt.show()

devices = ['Router1', 'Switch1', 'PC1', 'PC2']
connections = [('Router1', 'Switch1'), ('Switch1', 'PC1'), ('Switch1', 'PC2')]
generate_topology(devices, connections)
```

---

### **21. Whois Lookup**

**Objective:** Retrieve domain ownership and registration details.

**Purpose:** Perform domain analysis.

**Expected Output:** Whois information of a domain.

**Code:**
```python
import whois

def domain_whois(domain):
    try:
        info = whois.whois(domain)
        print(info)
    except Exception as e:
        print(f"Error retrieving whois information: {e}")

domain = input("Enter a domain name: ")
domain_whois(domain)
```

---

### **22. Test ICMP Reachability Using Scapy**

**Objective:** Send custom ICMP echo requests and analyze responses.

**Purpose:** Extend traditional ping functionality with `scapy`.

**Expected Output:** Custom ICMP packets sent and responses received.

**Code:**
```python
from scapy.all import sr1, IP, ICMP

def icmp_test(host):
    packet = IP(dst=host)/ICMP()
    response = sr1(packet, timeout=2, verbose=False)
    if response:
        print(f"Reply from {host}: {response.summary()}")
    else:
        print(f"No response from {host}")

host = input("Enter a host to test: ")
icmp_test(host)
```

---

### **23. Reverse DNS Lookup**

**Objective:** Retrieve the hostname for a given IP address.

**Purpose:** Perform reverse DNS analysis.

**Expected Output:** Hostname associated with the IP.

**Code:**
```python
import socket

def reverse_dns(ip):
    try:
        hostname = socket.gethostbyaddr(ip)
        print(f"Hostname for {ip}: {hostname[0]}")
    except socket.herror:
        print(f"Could not resolve hostname for {ip}")

ip_address = input("Enter an IP address: ")
reverse_dns(ip_address)
```

---

### **24. Generate Random MAC Address**

**Objective:** Create a random, valid MAC address.

**Purpose:** Simulate MAC address scenarios.

**Expected Output:** A randomly generated MAC address.

**Code:**
```python
import random

def generate_mac():
    mac = [random.randint(0x00, 0xFF) for _ in range(6)]
    return ':'.join(f"{octet:02x}" for octet in mac)

print(f"Random MAC Address: {generate_mac()}")
```

---

### **25. Check Open Proxy Status**

**Objective:** Test if a given proxy is operational.

**Purpose:** Validate proxy configurations.

**Expected Output:** Proxy status (working or not).

**Code:**
```python
import requests

def check_proxy(proxy):
    proxies = {
        "http": proxy,
        "https": proxy,
    }
    try:
        response = requests.get("http://ip-api.com/json", proxies=proxies, timeout=5)
        if response.status_code == 200:
            print(f"Proxy is working. IP: {response.json()['query']}")
        else:
            print("Proxy returned an error.")
    except Exception as e:
        print(f"Proxy failed: {e}")

proxy = input("Enter a proxy (e.g., http://1.2.3.4:8080): ")
check_proxy(proxy)
```

---

### **26. Calculate RTT for TCP Handshake**

**Objective:** Measure RTT (Round Trip Time) for a TCP handshake.

**Purpose:** Analyze connection establishment delays.

**Expected Output:** RTT in milliseconds.

**Code:**
```python
import socket
import time

def tcp_rtt(host, port):
    start_time = time.time()
    try:
        with socket.create_connection((host, port), timeout=5):
            rtt = (time.time() - start_time) * 1000
            print(f"RTT to {host}:{port} is {rtt:.2f} ms")
    except socket.timeout:
        print("Connection timed out.")

host = input("Enter target host: ")
port = int(input("Enter target port: "))
tcp_rtt(host, port)
```

---

### **27. Discover Default Gateway**

**Objective:** Identify the default gateway of the local network.

**Purpose:** Simplify gateway identification.

**Expected Output:** Default gateway IP.

**Code:**
```python
import netifaces

def default_gateway():
    gateway = netifaces.gateways().get('default', {})
    print(f"Default Gateway: {gateway.get(netifaces.AF_INET, [None])[0]}")

default_gateway()
```

---

### **28. Extract Wi-Fi SSIDs**

**Objective:** List available Wi-Fi networks (SSIDs).

**Purpose:** Discover local wireless networks.

**Expected Output:** List of detected SSIDs.

**Code:**
```python
import subprocess

def list_wifi_networks():
    try:
        result = subprocess.run(["nmcli", "-t", "-f", "SSID", "dev", "wifi"], stdout=subprocess.PIPE)
        networks = result.stdout.decode().split("\n")
        print("Available Wi-Fi Networks:")
        for network in networks:
            if network.strip():
                print(f"- {network.strip()}")
    except Exception as e:
        print(f"Error: {e}")

list_wifi_networks()
```

---

### **29. IP Address to Binary Converter**

**Objective:** Convert an IP address into its binary form.

**Purpose:** Understand IP address representation.

**Expected Output:** Binary equivalent of the IP.

**Code:**
```python
def ip_to_binary(ip):
    binary = ".".join(f"{int(octet):08b}" for octet in ip.split('.'))
    print(f"Binary representation of {ip}: {binary}")

ip_address = input("Enter an IPv4 address: ")
ip_to_binary(ip_address)
```

---

### **30. Monitor Network Bandwidth in Real-Time**

**Objective:** Monitor network bandwidth usage in real-time.

**Purpose:** Analyze current network load.

**Expected Output:** Bandwidth usage in Mbps.

**Code:**
```python
import psutil
import time

def monitor_bandwidth(interval=1):
    print("Monitoring bandwidth usage... Press Ctrl+C to stop.")
    old_values = psutil.net_io_counters()
    while True:
        time.sleep(interval)
        new_values = psutil.net_io_counters()
        sent = (new_values.bytes_sent - old_values.bytes_sent) / (1024 * 1024 * interval)
        recv = (new_values.bytes_recv - old_values.bytes_recv) / (1024 * 1024 * interval)
        print(f"Upload: {sent:.2f} Mbps, Download: {recv:.2f} Mbps")
        old_values = new_values

monitor_bandwidth()
```

---

#### **31. Retrieve and Analyze Routing Table**
**Objective:** Extract and analyze routing table entries.

**Code:**
```python
import os

def get_routing_table():
    result = os.popen("route -n").read()
    print("Routing Table:")
    print(result)

get_routing_table()
```

---

#### **32. Simulate Packet Loss**
**Objective:** Simulate packet loss using network tools.

**Code:**
```python
import os

def simulate_packet_loss(host, loss_percentage):
    command = f"ping -c 10 -i 0.2 -l 20 -f -q -w 5 --packet-loss {loss_percentage} {host}"
    result = os.popen(command).read()
    print(result)

host = input("Enter target host: ")
simulate_packet_loss(host, 10)
```

---

#### **33. Custom Traceroute**
**Objective:** Implement a Python-based traceroute.

**Code:**
```python
from scapy.all import *

def custom_traceroute(destination):
    for ttl in range(1, 30):
        pkt = IP(dst=destination, ttl=ttl) / ICMP()
        reply = sr1(pkt, verbose=False, timeout=1)
        if reply is None:
            print(f"{ttl} * * *")
        elif reply.type == 0:
            print(f"{ttl} {reply.src} (Destination Reached)")
            break
        else:
            print(f"{ttl} {reply.src}")

destination = input("Enter destination host: ")
custom_traceroute(destination)
```

---

#### **34. Validate SSL Certificate**
**Objective:** Verify the SSL certificate of a website.

**Code:**
```python
import ssl
import socket

def validate_ssl(host, port=443):
    context = ssl.create_default_context()
    with socket.create_connection((host, port)) as sock:
        with context.wrap_socket(sock, server_hostname=host) as ssock:
            cert = ssock.getpeercert()
            print(f"SSL Certificate for {host}:")
            for key, value in cert.items():
                print(f"{key}: {value}")

host = input("Enter website host: ")
validate_ssl(host)
```

---

#### **35. Backup Network Device Configurations**
**Objective:** Backup device configurations via SSH.

**Code:**
```python
from netmiko import ConnectHandler

def backup_config(device):
    connection = ConnectHandler(**device)
    config = connection.send_command("show running-config")
    with open(f"{device['host']}_backup.txt", "w") as file:
        file.write(config)
    print(f"Configuration backed up for {device['host']}")
    connection.disconnect()

device = {
    'device_type': 'cisco_ios',
    'host': '192.168.1.1',
    'username': 'admin',
    'password': 'password',
}
backup_config(device)
```

---
