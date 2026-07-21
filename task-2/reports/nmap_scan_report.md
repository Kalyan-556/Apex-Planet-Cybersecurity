# Nmap Service & Vulnerability Scan Report

* **Target System IP Address:** `192.168.56.102`
* **Target Operating System:** Linux 2.6.x (Metasploitable2 target)
* **Attacker System IP:** `192.168.56.101` (Kali Linux)
* **Scan Command Used:** `sudo nmap -sS -sV -O -F 192.168.56.102`

---

## 📋 Open Ports & Discovered Services

| Port / Protocol | State | Service | Service Version | Security Risk Level |
| :--- | :--- | :--- | :--- | :--- |
| **21 / TCP** | Open | FTP | vsftpd 2.3.4 | 🚨 **CRITICAL** |
| **22 / TCP** | Open | SSH | OpenSSH 4.7p1 (Protocol 2.0) | ⚠️ **MEDIUM** |
| **23 / TCP** | Open | Telnet | Linux telnetd | 🔴 **HIGH** |
| **25 / TCP** | Open | SMTP | Postfix smtpd | ⚠️ **MEDIUM** |
| **53 / TCP** | Open | DNS | BIND 9.4.2 | ⚠️ **MEDIUM** |
| **80 / TCP** | Open | HTTP | Apache httpd 2.2.8 (PHP/5.2.4) | 🔴 **HIGH** |
| **139 / TCP** | Open | NetBIOS | Samba smbd 3.x - 4.x | 🚨 **CRITICAL** |
| **445 / TCP** | Open | SMB | Samba smbd 3.0.20-Debian | 🚨 **CRITICAL** |
| **3306 / TCP**| Open | MySQL | MySQL 5.0.51a-3ubuntu5 | ⚠️ **MEDIUM** |
| **5432 / TCP**| Open | PostgreSQL | PostgreSQL 8.3.0 - 8.3.7 | 🔴 **HIGH** |
| **8180 / TCP**| Open | HTTP-Proxy | Apache Tomcat/Coyote 1.1 | 🚨 **CRITICAL** |

---

## 🔍 Detailed Analysis of Vulnerable Services

### 1. Port 21: FTP (vsftpd 2.3.4)
* **Risk:** Command execution / Backdoor.
* **CVE Identification:** CVE-2011-2523.
* **Analysis:** The specific version of vsftpd (2.3.4) installed on the target contains a notorious backdoor trigger. Sending a username ending with a smiley face (e.g. `user kalyan:)`) and any password opens a shell listener on port `6200` with root privileges. 
* **Remediation:** Immediately update vsftpd to the latest secure version, or disable FTP in favor of SFTP (SSH File Transfer Protocol).

### 2. Port 23: Telnet
* **Risk:** Plaintext credential exposure / Sniffing.
* **Analysis:** Telnet transmits all data, including usernames, passwords, and shell outputs, in unencrypted plaintext. Anyone sniffing local network packets can retrieve authorization credentials easily.
* **Remediation:** Disable the telnet service entirely (`systemctl disable inetd`). Use SSH for all remote command access.

### 3. Port 80: Apache HTTPD (PHP 5.2.4)
* **Risk:** Outdated server configuration / Web app vulnerabilities.
* **Analysis:** The Apache web server is running PHP 5.2.4, which is outdated and vulnerable to several Remote Code Execution (RCE) flaws. Furthermore, it hosts vulnerable web apps (DVWA, Mutillidae) containing SQL injection and XSS vulnerabilities.
* **Remediation:** Upgrade Apache and PHP to current stable releases. Implement security hardening in `php.ini` (e.g., disable `allow_url_fopen` and `allow_url_include`).

### 4. Ports 139 / 445: Samba SMB (3.0.20-Debian)
* **Risk:** Remote Command Execution.
* **CVE Identification:** CVE-2007-2447 (`usermap_script`).
* **Analysis:** This Samba version contains a configuration flaw where user input parameters in username mapping scripts are executed directly in a shell. Passing shell metacharacters in the username parameter allows arbitrary command execution as root.
* **Remediation:** Upgrade Samba to the latest secure release, or disable SMB services if files are not being shared with Windows hosts.

### 5. Port 8180: Apache Tomcat (Tomcat 5.5)
* **Risk:** Unauthorized Administrative Deployment.
* **Analysis:** Tomcat has its manager interface exposed. Common default administrator credentials (e.g., `admin/admin` or `tomcat/tomcat`) are enabled, allowing attackers to upload a malicious `.war` payload to gain remote code execution on the system.
* **Remediation:** Change default credentials immediately, disable the manager application on production instances, and restrict management interface access by source IP.

---

## 📌 Summary Recommendations
1. **Apply Network Access Control:** Use firewalls to restrict access to ports like database systems (MySQL, PostgreSQL) and Samba.
2. **Decommission Legacy Protocols:** Turn off insecure plaintext protocols like Telnet and Rlogin.
3. **Upgrade Core Infrastructure:** Major operating system components and servers (Apache, PHP, Samba, FTP) must be updated to patch known CVEs.
