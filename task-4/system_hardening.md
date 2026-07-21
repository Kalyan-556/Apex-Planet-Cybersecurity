# Linux Server Hardening Checklist

This document provides an actionable, production-grade checklist used to secure and harden Linux systems against unauthorized access, exploitation, and malware persistence.

---

## 📅 1. System Updates & Patch Management
* [ ] **Automate Security Updates:** Configure `unattended-upgrades` to automatically fetch and apply security patches.
  ```bash
  sudo apt install unattended-upgrades
  sudo dpkg-reconfigure --priority=low unattended-upgrades
  ```
* [ ] **Kernel Upgrades:** Keep the Linux kernel updated to mitigate local privilege escalation (LPE) exploits.

---

## 🔑 2. User Authentication & SSH Hardening
Configure `/etc/ssh/sshd_config` to enforce secure remote management policies:
* [ ] **Disable Root SSH Logins:** Prevent direct root log-ins; force users to authenticate as a standard user first, then use `sudo`.
  ```ini
  PermitRootLogin no
  ```
* [ ] **Disable Password Authentication:** Enforce RSA/Ed25519 key-based authentication.
  ```ini
  PasswordAuthentication no
  ```
* [ ] **Change SSH Port:** Relocate SSH from the default port 22 to a non-standard port (e.g., `2222`) to stop automated scanning bots.
  ```ini
  Port 2222
  ```
* [ ] **Configure Idle Timeout:** Disconnect inactive SSH sessions automatically.
  ```ini
  ClientAliveInterval 300
  ClientAliveCountMax 0
  ```
* [ ] **Password Policies:** Edit `/etc/security/pwquality.conf` to mandate password complexity (minimum 14 characters, numbers, uppercase/lowercase, and special symbols).
* [ ] **Lock Inactive Accounts:** Automatically disable accounts that are inactive for 30 days.

---

## 🧱 3. Host Firewall & Network Hardening
* [ ] **Restrict Network Interfaces:** Implement strict firewall settings via `UFW` or `iptables` to block all incoming traffic except explicitly permitted service ports (e.g., HTTP/HTTPS).
* [ ] **Secure sysctl Network Settings:** Edit `/etc/sysctl.conf` to mitigate network-based spoofing and reconnaissance:
  ```ini
  # Ignore ICMP echo requests (blocks Ping sweeps)
  net.ipv4.icmp_echo_ignore_all = 1

  # Disable source routing packet acceptance
  net.ipv4.conf.all.accept_source_route = 0
  net.ipv4.conf.default.accept_source_route = 0

  # Turn on SYN Cookie protection (mitigates TCP SYN Floods)
  net.ipv4.tcp_syncookies = 1

  # Log martian packets (packets with impossible source addresses)
  net.ipv4.conf.all.log_martians = 1
  ```

---

## 🧰 4. Minimizing the Service Attack Surface
* [ ] **Identify Listening Ports:** Scan for active network daemons running on the system:
  ```bash
  sudo ss -tulpn
  ```
* [ ] **Stop & Remove Unused Services:** Uninstall deprecated or unnecessary services (e.g., FTP, Telnet, Rlogin, Samba, NFS) to minimize target vectors:
  ```bash
  sudo systemctl stop vsftpd
  sudo systemctl disable vsftpd
  sudo apt purge vsftpd
  ```
* [ ] **Implement Rate Limiting:** Set up `Fail2Ban` to automatically ban IP addresses that generate excessive authentication errors (e.g. brute-force SSH attempts).
  ```bash
  sudo apt install fail2ban
  sudo systemctl enable fail2ban
  ```

---

## 📝 5. Auditing & Active Logging
* [ ] **System Event Auditing:** Set up `auditd` to track writes to sensitive files (like `/etc/passwd` or `/etc/shadow`) and record user execution privileges.
  ```bash
  sudo apt install auditd
  sudo systemctl enable auditd
  ```
* [ ] **Centralized Logging:** Configure `rsyslog` to forward system and authorization logs (`/var/log/auth.log`) to a secure remote SIEM server.
* [ ] **Check File Integrity:** Install `AIDE` (Advanced Intrusion Detection Environment) to run daily cron jobs checking for modifications in system binary hashes (like `/bin`, `/sbin`).
