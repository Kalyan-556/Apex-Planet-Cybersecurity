# Task 5: Capstone Project & Incident Response

## 🎯 Objective
To apply all acquired cybersecurity skills in a comprehensive Capstone Project—specifically, a professional Web Application Penetration Test—and design an Incident Response Plan to handle, contain, and resolve active security incidents.

---

## 🛡️ Capstone Project: Web Application Penetration Test Report
* **Target System:** Damn Vulnerable Web Application (DVWA v1.9)
* **Author:** Kalyan Katta
* **Organization:** ApexPlanet Software Pvt. Ltd.
* **Scope:** 192.168.56.102 (Web Port 80 / Tomcat Port 8180)

### 1. Executive Summary
During this security assessment, a comprehensive vulnerability analysis and exploitation test was executed against the DVWA host. The evaluation aimed to identify OWASP Top 10 web application vulnerabilities and determine the potential business impact of an unauthorized system compromise. 

The assessment revealed multiple **Critical** flaws, notably Remote File Inclusion and SQL Injection, which allowed command execution and full database access. Security controls must be implemented immediately to remediate these issues.

---

### 2. Penetration Testing Methodology
The assessment was executed in four sequential phases:
1. **Reconnaissance:** Scanning open ports and mapping host configurations using Nmap.
2. **Vulnerability Assessment:** Automating vulnerability scans via Nessus/OpenVAS to detect known configuration issues and CVEs.
3. **Exploitation:** Executing manual injection attacks and automated exploitation via Burp Suite/Metasploit to prove susceptibility.
4. **Analysis & Remediation:** Compiling finding logs, assessing business impact, and specifying secure-coding fixes.

---

### 3. Vulnerability Findings & Severity Matrix

| ID | Vulnerability | Severity | Impact | CVE | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **V-01** | SQL Injection (Data Extraction) | 🚨 **Critical** | Database takeover & credential exposure | N/A | Open |
| **V-02** | Remote File Inclusion (RFI) | 🚨 **Critical** | Remote Code Execution (RCE) on web server | N/A | Open |
| **V-03** | Local File Inclusion (LFI) | 🔴 **High** | Reading system files (e.g. `/etc/passwd`) | N/A | Open |
| **V-04** | Stored Cross-Site Scripting (XSS) | 🔴 **High** | Session hijacking of authenticated users | N/A | Open |
| **V-05** | Weak/Default Credentials (SSH) | ⚠️ **Medium** | Unauthorized terminal access | N/A | Open |
| **V-06** | Missing Security Response Headers | ℹ️ **Low** | Clickjacking / MIME Type Sniffing | N/A | Open |

---

### 4. Vulnerability Deep Dive & Impact

#### V-01: SQL Injection (SQLi)
* **Finding:** Input fields fail to filter special query characters.
* **Impact:** An attacker can extract the entire database schema, user accounts, and hashed passwords, leading to data loss and unauthorized administrative entry.
* **Remediation:** Enforce the use of PDO prepared statements with parameterized queries.

#### V-02: Remote File Inclusion (RFI)
* **Finding:** The application allows external URL references in file execution functions.
* **Impact:** Attackers can inject and execute web shells hosted on remote servers, leading to full web server shell access and lateral movement.
* **Remediation:** Set `allow_url_include = Off` in the PHP configuration (`php.ini`) and restrict file inputs to a whitelist.

#### V-03: Stored Cross-Site Scripting (XSS)
* **Finding:** Input signatures are written directly to database fields and printed to the visitor guestbook unescaped.
* **Impact:** Allows execution of arbitrary JavaScript in visitors' browsers, enabling cookie sniffing, session hijacking, and defacement.
* **Remediation:** Use HTML entity encoding (`htmlspecialchars()`) on all dynamic content output.

---

## 🚨 Incident Response Simulation
In the event of an active intrusion (such as a Web Shell upload or a SYN Flood DDoS attack), organizations must execute a systematic response.

An operational Incident Response workflow has been designed to outline immediate actions for detection, containment, eradication, and lessons learned:
* 📄 [Incident Response & Threat Eradication Plan](./incident_response_plan.md)

---

## 🎥 Task 5 Final Video Presentation
> LinkedIn video presentation showcasing the final Capstone Project Web Penetration Test Report and Incident Response plan:
> * **LinkedIn Final Presentation Video:** [Task 5 - Capstone & Incident Response](https://www.linkedin.com/posts/kalyan-katta-6b491338b_cybersecurity-ethicalhacking-capstoneproject-activity-7485346451292745729-b4rA?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGAQxSgBWlNdbkRaK_lHTiAJO0x0AIxoNAo)
