# Linux & Networking fundamentals Cheat-Sheet

This cheat-sheet documents the core terminal commands, directory permissions, package operations, and networking utilities mastered during the first phase of the internship.

---

## 📁 File System Navigation & Operations

| Command | Action | Example |
| :--- | :--- | :--- |
| `pwd` | Print current working directory | `pwd` |
| `ls -la` | List all files (including hidden ones) with sizes & permissions | `ls -la /var/www/html` |
| `cd` | Change directory | `cd /etc/network` |
| `mkdir -p` | Create directory structure recursively | `mkdir -p ~/projects/sec/scans` |
| `rm -rf` | Force remove files or directories recursively (use with caution) | `rm -rf ~/temp_logs` |
| `cp -r` | Copy files or directories recursively | `cp -r /etc/apache2 ~/backups/` |
| `mv` | Move or rename files | `mv scan.txt archive_scan.txt` |
| `cat` | View file content | `cat /etc/passwd` |
| `grep` | Search text using patterns | `grep "FAILED" /var/log/auth.log` |
| `nano` / `vim` | Terminal-based text editors | `nano /etc/hosts` |

---

## 🔐 File Permissions & Ownership

Linux security utilizes a strict ownership/permission scheme: User, Group, and Others.

### chmod (Change Mode)
Modifies file/directory read (`r` = 4), write (`w` = 2), and execute (`x` = 1) permissions.
* **Octal Notation:**
  * `chmod 755 script.sh` (rwxr-xr-x: owner can read/write/execute; others can read/execute)
  * `chmod 600 id_rsa` (rw-------: only owner can read/write - standard for private SSH keys)
  * `chmod +x run.sh` (Adds execution permission to all users)

### chown (Change Owner)
Changes user and/or group ownership of a file:
* `chown root:root secret_key.bin` (Set owner to root and group to root)
* `chown -R kalyan:kalyan /home/kalyan/project` (Recursively modify directory ownership)

---

## 📦 Package Management (APT)

Standard commands for Debian-based distributions like Kali Linux:
```bash
# Update local package index lists
sudo apt update

# Upgrade all installed packages to current versions
sudo apt upgrade -y

# Install security and network tools
sudo apt install -y nmap wireshark burpsuite netcat-traditional

# Uninstall a package
sudo apt remove netcat-traditional
```

---

## 🌐 Networking Utilities

Used to inspect networks, look up domains, check routing tables, and verify adapter status.

* **Check Network Adapters & IP Address:**
  ```bash
  ip address show   # Modern command
  ifconfig          # Legacy command
  ```
* **Verify Connectivity:**
  ```bash
  ping -c 4 192.168.56.102   # Ping target machine 4 times
  ```
* **Trace Route Path:**
  ```bash
  traceroute 8.8.8.8
  ```
* **View Ports & Active Connections:**
  ```bash
  netstat -antp     # List TCP ports and their associated PID/program name
  ss -tulpn         # Modern socket statistics display
  ```
* **DNS Resolution Lookup:**
  ```bash
  nslookup targetsite.com
  dig targetsite.com
  ```

---

## 🖧 OSI Model vs. TCP/IP Protocol Suite

Understanding network stacks is crucial for parsing packet captures during traffic analysis.

| Layer # | OSI Model Layer | Core Functions | TCP/IP Layer | Associated Protocols |
| :--- | :--- | :--- | :--- | :--- |
| **7** | Application | Network services to applications | **Application** | HTTP, HTTPS, FTP, DNS, SSH, SMTP |
| **6** | Presentation | Data representation, encryption | **Application** | SSL, TLS, JPEG, ASCII |
| **5** | Session | Inter-host communication sessions | **Application** | NetBIOS, RPC |
| **4** | Transport | End-to-end connections, reliability | **Transport** | TCP, UDP |
| **3** | Network | Path determination, logical addressing | **Internet** | IP (IPv4/IPv6), ICMP, ARP |
| **2** | Data Link | Physical addressing (MAC), frames | **Network Access** | Ethernet, Wi-Fi (802.11), PPP |
| **1** | Physical | Binary transmission, cables, voltage | **Network Access** | Ethernet physical layer, fiber |
