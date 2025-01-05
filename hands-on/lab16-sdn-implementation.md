# Lab 16: Implementing SDN (Software-Defined Networking)

## **Objective**
- Understand SDN concepts and the role of controllers.
- Configure an SDN-enabled network using OpenFlow.
- Use OpenDaylight to manage the network centrally.

---

## **Scenario**
Your organization is transitioning to SDN to achieve centralized network control and flexibility. You need to:
1. Set up an OpenFlow-enabled network.
2. Configure an OpenDaylight controller to manage devices.
3. Verify and test centralized traffic control.

---

## **Step-by-Step Instructions**

### **Step 1: Set Up the Lab Environment**

1. Use a virtualized environment or Mininet, an SDN network emulator.
2. Install the OpenDaylight SDN controller:
   - Download and install OpenDaylight:
     ```bash
     wget https://nexus.opendaylight.org/releases/org/opendaylight/integration/distribution-karaf/15.0.0/distribution-karaf-15.0.0.tar.gz
     tar -xvzf distribution-karaf-15.0.0.tar.gz
     cd distribution-karaf-15.0.0
     ./bin/karaf
     ```
   - Install the OpenFlow feature:
     ```bash
     feature:install odl-openflowplugin-all
     ```

3. Use Mininet to create an SDN-enabled network:
   ```bash
   sudo mn --controller=remote,ip=<controller-ip> --switch=ovs,protocols=OpenFlow13 --topo=tree,depth=2
   ```

---

### **Step 2: Configure OpenFlow on the Switch**
1. Enable OpenFlow on switches in Mininet:
   ```bash
   ovs-vsctl set bridge s1 protocols=OpenFlow13
   ovs-vsctl set-controller s1 tcp:<controller-ip>:6633
   ```
2. Verify the OpenFlow connection:
   ```bash
   ovs-vsctl show
   ```

---

### **Step 3: Configure Flows Using OpenDaylight**
1. Log in to the OpenDaylight GUI (default port `8181`):
   - URL: `http://<controller-ip>:8181`
   - Default credentials: `admin/admin`.

2. Add a flow to forward traffic:
   - Match on specific source IP (e.g., Dev: `192.168.10.0/24`).
   - Forward traffic to a specific port:
     - In the GUI, go to **Flows** > **Add Flow**.
     - Configure:
       - **Match**: Source IP = `192.168.10.2`
       - **Action**: Output to Port 2.

3. Test traffic control:
   - Ping between hosts in Mininet:
     ```bash
     ping 192.168.10.3
     ```

---

### **Step 4: Test Centralized Traffic Management**
1. Modify flow rules dynamically:
   - Block traffic from QA (192.168.20.0/24) to Production (192.168.40.0/24):
     - In the OpenDaylight GUI, add a flow to drop packets:
       - **Match**: Source IP = `192.168.20.0/24`.
       - **Action**: Drop.
   - Verify using Mininet:
     ```bash
     h1 ping h4
     ```

2. Analyze OpenFlow traffic using Wireshark:
   - Filter for OpenFlow packets:
     ```bash
     tcp.port == 6633
     ```

---

## **Hands-On Challenges**

1. **Dynamic Traffic Control:**
   - Use the OpenDaylight REST API to add, modify, and delete flows programmatically:
     ```bash
     curl -X POST -H "Content-Type: application/json" -u admin:admin \
     -d '{"flow": { "priority": "100", "match": {"ipv4-source": "192.168.10.2/32"}, "instructions": { "instruction": [{"order": 0, "apply-actions": {"action": [{"output-action": {"output-node-connector": "2"}}]}}]}}}' \
     http://<controller-ip>:8181/restconf/operations/sal-flow:add-flow
     ```

2. **Simulate Network Failures:**
   - Disable a switch port in Mininet and observe traffic rerouting:
     ```bash
     sudo ovs-vsctl set interface s1-eth1 admin_state=down
     ```

3. **Traffic Monitoring:**
   - Use OpenDaylight's telemetry features to monitor traffic flows in real-time.

---

## **Expected Outcome**
- OpenDaylight centrally manages the SDN-enabled network.
- Traffic flows are dynamically controlled and modified using OpenFlow rules.
- Wireshark captures validate OpenFlow communication between switches and the controller.

---

## **Key Takeaways**
- SDN separates the control plane and data plane for centralized network management.
- OpenFlow is a key protocol for implementing SDN.
- Controllers like OpenDaylight provide programmability and scalability for modern networks.

---
