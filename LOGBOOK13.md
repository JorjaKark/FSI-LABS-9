# **LOGBOOK 13 – Packet Sniffing and Spoofing Lab**

This logbook documents the experiments performed in the **SEED Packet Sniffing and Spoofing Lab**.  
The objective of this lab is to understand how packet sniffing and packet spoofing work at the network level, using raw sockets, Scapy, and Wireshark, and to analyze how improper network protections can allow an attacker to observe or manipulate network traffic.

All experiments were conducted inside a controlled Docker-based environment provided by the SEED Labs framework.

---

## **Environment Setup**

### **Objective**

The objective of this setup phase is to create a controlled virtual network environment containing multiple hosts and an attacker machine. This environment allows the observation, interception, and manipulation of network traffic in a realistic but isolated setting.

The lab environment consists of three Docker containers connected to the same subnet:

- **Host A** – a normal user host
- **Host B** – another normal user host
- **Attacker** – a privileged container capable of packet sniffing and spoofing

---

### **Container Deployment**

The lab environment was initialized using the provided `docker-compose.yml` file located in the `Labsetup-arm` directory.

The following commands were executed to validate the Docker configuration and launch the containers:

```bash
docker compose config --services
docker compose up -d
docker ps
```

These commands verified the defined services and started all containers in detached mode.

**Screenshot:**
![Figure 1](./screenshots/screenshots-week13/setup/docker-containers-setup.png)

**Figure 1** – Docker containers successfully created and running (`hostA`, `hostB`, and `attacker`).

The output confirms that all three containers were running correctly and ready for interaction.

---

### **Network Configuration**

After starting the containers, the Docker networks were inspected using:

```bash
docker network ls
```

**Screenshot:**
![Figure 2](./screenshots/screenshots-week13/setup/docker-network-ls.png)

**Figure 2** – Docker network list showing the custom bridge network `net-10.9.0.0`.

The output shows a custom bridge network named `net-10.9.0.0`, which corresponds to the subnet used by the lab environment. This network isolates the containers and enables controlled communication between them.

---

### **Interface Verification**

To identify the network interface associated with the Docker bridge, the following command was executed on the host VM:

```bash
ifconfig
```

**Screenshot:**
![Figure 3](./screenshots/screenshots-week13/setup/ifconfig.png)

**Figure 3** – Network interface `br-9eaacd4259b` associated with subnet `10.9.0.0/24`.

The interface `br-9eaacd4259b` was assigned the IP address `10.9.0.1`, acting as the gateway for the container network.
This interface is later used in packet sniffing experiments, as it carries all traffic exchanged between the containers.

---

### **Container Access Verification**

To confirm that the containers were accessible and interactive, a shell session was opened inside **Host A** using:

```bash
docker exec -it hostA-10.9.0.5 bash
```

The session was exited immediately after confirming access.

**Screenshot:**
![Figure 4](./screenshots/screenshots-week13/setup/running-shell-hostA-test.png)

**Figure 4** – Successful interactive shell session inside Host A container.

This confirms that the containers were correctly deployed and ready for use in subsequent packet sniffing and spoofing tasks.

---

### **Summary**

At the end of the setup phase:

* All containers were running successfully
* A custom Docker bridge network (`10.9.0.0/24`) was created
* The bridge interface was identified for packet capture
* Interactive access to containers was confirmed

This environment provides a realistic and isolated platform for experimenting with packet sniffing, spoofing, and traffic analysis in the following tasks.

---

## **Task 1.1 A – Packet Sniffing**

### **Objective**

The objective of Task 1.1 A is to observe ICMP traffic exchanged between two hosts located on the same subnet using a packet sniffer implemented with **Scapy**.
This task demonstrates how an attacker with sufficient privileges can capture and inspect network packets traversing a shared network interface.

---

### **Sniffer Preparation**

The provided `sniffer.py` script was used to capture packets at the Ethernet level.
Before execution, execution permissions were granted to the script:

```bash
chmod a+x sniffer.py
```

The sniffer was then executed with root privileges, which are required to open raw sockets:

```bash
sudo ./sniffer.py
```

**Screenshot:**
![Figure 5](./screenshots/screenshots-week13/task1.1/A/chmod+run-sniffer-py.png)

**Figure 5** – Granting execution permissions and running the sniffer with root privileges.

---

### **ICMP Traffic Generation**

To generate observable traffic, ICMP echo requests were sent between **Host A** and **Host B**.

From **Host A** (`10.9.0.5`) to **Host B** (`10.9.0.6`):

```bash
docker exec -it hostA-10.9.0.5 ping -c 2 10.9.0.6
```

**Screenshot:**
![Figure 6](./screenshots/screenshots-week13/task1.1/A/hostA-ping-hostB.png)

**Figure 6** – ICMP echo requests sent from Host A to Host B.

From **Host B** to **Host A**:

```bash
docker exec -it hostB-10.9.0.6 ping -c 2 10.9.0.5
```

**Screenshot:**
![Figure 7](./screenshots/screenshots-week13/task1.1/A/hostB-ping-hostA.png)

**Figure 7** – ICMP echo requests sent from Host B to Host A.

---

### **Sniffer Configuration**

The sniffer was configured to listen on the Docker bridge interface (`br-9eaacd4259b`) and filter ICMP packets only.

The contents of `sniffer.py` are shown below:

```python
#!/usr/bin/env python3
from scapy.all import *

def print_pkt(pkt):
    pkt.show()

pkt = sniff(iface='br-9eaacd4259b', filter='icmp', prn=print_pkt)
```

**Screenshot:**
![Figure 8](./screenshots/screenshots-week13/task1.1/A/nano-sniffer-py.png)

**Figure 8** – ICMP sniffer implemented using Scapy.

---

### **Packet Capture Results**

While the sniffer was running, ICMP packets exchanged between Host A and Host B were successfully captured and displayed.

Captured packets from **Host A → Host B**:

**Screenshot:**
![Figure 9](./screenshots/screenshots-week13/task1.1/A/packets-capture-sniffer-A-B.png)

![Figure 10](./screenshots/screenshots-week13/task1.1/A/packets-capture-sniffer-A-B-2.png)

**Figure 9–10** – ICMP echo request and reply packets captured from Host A to Host B.

Captured packets from **Host B → Host A**:

**Screenshot:**
![Figure 11](./screenshots/screenshots-week13/task1.1/A/packets-capture-sniffer-B-A.png)

![Figure 12](./screenshots/screenshots-week13/task1.1/A/packets-capture-sniffer-B-A-2.png)

**Figure 11–12** – ICMP echo request and reply packets captured from Host B to Host A.

The captured output includes full Ethernet, IP, and ICMP headers, confirming that the sniffer operates at the raw packet level.

---

### **Privilege Requirement Verification**

To verify that raw packet sniffing requires elevated privileges, the sniffer was executed **without** `sudo`:

```bash
./sniffer.py
```

This resulted in a permission error, confirming that raw sockets are restricted to privileged users.

**Screenshot:**
![Figure 13](./screenshots/screenshots-week13/task1.1/A/sniffer-no-root-priv.png)

**Figure 13** – Sniffer execution without root privileges fails due to insufficient permissions.

---

### **Summary**

In Task 1.1 A:

* ICMP traffic between two hosts on the same subnet was successfully generated
* A Scapy-based sniffer captured packets on the Docker bridge interface
* Full packet headers were inspected in real time
* Root privileges were confirmed as mandatory for raw packet sniffing

This task demonstrates how an attacker on the same network can passively observe traffic if proper isolation and protections are not enforced.

---
