# Task 2: Network Security & Scanning

## 🎯 Objective
To master reconnaissance methodologies, perform active and passive scanning against target infrastructure, analyze network traffic, identify security vulnerabilities, and implement basic host-level firewalls.

---

## 🔍 1. Reconnaissance (Passive & Active)

Reconnaissance is the initial phase of security testing, gathering vital information about the target.

### Passive Reconnaissance
Passive recon gathers data without directly sending packets to target servers:
* **Whois Lookup:** Retrieved registrar information, creation dates, and server configurations for evaluation.
* **Nslookup / DNS Interrogation:** Queried mail exchanges (`MX`), name servers (`NS`), and canonical names (`CNAME`) of target domains.
* **Google Dorking:** Used advanced operators (e.g., `inurl:admin`, `filetype:sql`, `intitle:"index of"`) to discover exposed directories and sensitive login portals.
* **Shodan search:** Analyzed public databases to find open services and default firmware versions of internet-facing devices.

### Active Reconnaissance
Active recon interacts directly with the target to identify active systems:
* **Ping Sweep:** Mapped alive hosts on the local host-only subnet (`192.168.56.0/24`) using Nmap:
  ```bash
  nmap -sn 192.168.56.0/24
  ```

---

## 🛡️ 2. Port & Service Scanning (Nmap)

Using Nmap, the target system `192.168.56.102` was scanned to discover open ports, running software versions, and operating system details.

* **TCP SYN Stealth Scan (`-sS`):** Scans quickly by not completing the TCP 3-way handshake.
* **Service Version Detection (`-sV`):** Probes open ports to identify application names and versions.
* **OS Detection (`-O`):** Analyzes TCP/IP stack fingerprints to identify the operating system.
* **Full Port Scan (`-p-`):** Examines all 65,535 TCP ports.

### Scan Execution Command:
```bash
sudo nmap -sS -sV -O -p- 192.168.56.102
```

> [!NOTE]
> The detailed results, port lists, and vulnerability analysis of this scan are documented in:
> 📄 [Nmap Scan Report](./reports/nmap_scan_report.md)

---

## ⚡ 3. Vulnerability Scanning (Nessus Essentials)

A Nessus vulnerability scanner was configured to analyze the target VM (`192.168.56.102`) for known CVEs.

### Analysis of Findings:
1. **Critical Vulnerabilities:** Exposed backdoor interfaces (e.g., VSFTPD 2.3.4 backdoor), anonymous FTP logins with write permissions, and outdated Bind/Samba versions.
2. **High Vulnerabilities:** Weak SSH key exchange configurations, unencrypted Telnet/Rlogin availability, and Java RMI Registry exposure.
3. **Remediation Actions:** Nessus results highlight that disabling legacy services (FTP, Telnet) and updating Samba/Bind configurations would eliminate over 80% of the critical attack surface.

---

## 📈 4. Packet Analysis with Wireshark & hping3

Network traffic was captured and analyzed on the Host-Only interface to understand data transit and attack behaviors.

### Capture Plaintext Credentials (FTP)
* **Goal:** Demonstrate the security risk of unencrypted protocols.
* **Step:** Wireshark capture filters were set to `ftp`. A login session was recorded.
* **Analysis:** Because FTP is plaintext, the username (`msfadmin`) and password (`msfadmin`) were fully visible in the TCP stream packet:
  ```
  220 (vsFTPd 2.3.4)
  USER msfadmin
  331 Please specify the password.
  PASS msfadmin
  230 Login successful.
  ```

### SYN Flood Attack Simulation
* **Goal:** Simulate a Denial of Service (DoS) attack and analyze the packet signature.
* **Attack Tool (hping3):** Run from Kali Linux to flood the target web server with TCP SYN packets:
  ```bash
  sudo hping3 -S -p 80 --flood --rand-source 192.168.56.102
  ```
* **Wireshark Analysis:** Captured traffic showed a flood of TCP SYN packets from randomized source IPs to port 80. The target sent SYN-ACK packets, but since the source IPs were randomized, they remained "half-open" (waiting for ACK), exhausting the target's connection queue (TCP backlog buffer saturation).

---

## 🧱 5. Host Firewall Configuration (iptables)

Basic host hardening was performed using `iptables` rules.

### Setup Rules to Secure the Target:
```bash
# Set default policies to DROP incoming traffic
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT

# Allow established and related connections (so we don't break existing active sessions)
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Allow loopback traffic (localhost)
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow inbound SSH (Port 22) from a trusted management workstation IP
sudo iptables -A INPUT -p tcp -s 192.168.56.101 --dport 22 -j ACCEPT

# Allow inbound HTTP (Port 80) for web traffic
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

---

## 🎥 Task 2 Video Demonstration
> [!TIP]
> Paste your LinkedIn video link or Google Drive link below once uploaded:
> * **LinkedIn Video Scan Demo:** `[Link to LinkedIn Video]`
> * **Alternative Drive Video:** `[Link to Google Drive / YouTube]`
