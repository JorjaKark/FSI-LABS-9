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


