# Incident Response & Threat Eradication Plan

This Incident Response (IR) plan outlines the technical and procedural framework to detect, contain, eradicate, and recover from security incidents, aligned with the NIST SP 800-61 standard.

---

## 🔄 The 6 Phases of Incident Response

```
┌───────────────┐     ┌───────────────────┐     ┌───────────────┐
│ 1.Preparation │ ──> │2.Detection/Analys.│ ──> │ 3.Containment │
└───────────────┘     └───────────────────┘     └───────────────┘
                                                        │
┌───────────────┐     ┌───────────────────┐             ▼
│6.Lessons Lear.│ <── │    5. Recovery    │ <── │4. Eradication │
└───────────────┘     └───────────────────┘     └───────────────┘
```

---

## 🛠️ Phase-by-Phase Technical Playbook

### Phase 1: Preparation
* **Policy & Team:** Establish clear contact lists and roles (IR Lead, SysAdmin, Network Engineer).
* **Tooling:** Deploy detection agents, centralized logging (rsyslog/SIEM), and packet capture configurations.
* **Backup Strategy:** Enforce secure, offsite, read-only configuration backups.

---

### Phase 2: Detection & Analysis
* **Anomalous Signatures:** High CPU utilization, sudden surges in outbound bandwidth, or persistent connection requests.
* **Log Inspections:** Search web server audit logs (`/var/log/apache2/access.log`) for exploitation scripts, status code errors (500/404 floods), or SQL query parameters.
* **Network Detections:** Use tools like Wireshark or tcpdump to analyze active packet streams and identify malicious traffic sources.

---

### Phase 3: Containment
Isolate compromised systems to prevent further network damage or lateral movement.

#### Short-Term Containment Action:
1. **Network Isolation:** Disconnect the affected VM's network interface (virtual Ethernet cable) to disconnect the attacker while preserving memory state for forensic analysis.
2. **IP Blocking:** Deploy local firewall rules to drop traffic from the attacking host.
   ```bash
   # Block the specific IP address of the attacker
   sudo iptables -A INPUT -s 192.168.56.101 -j DROP
   ```

#### Long-Term Containment:
Re-route routing tables, update external firewall rules, and revoke active user API keys/passwords.

---

### Phase 4: Eradication
Locate and safely remove all threats from the system, resolving the underlying vulnerability.

1. **Remove Backdoors:** Find and delete uploaded web shell files:
   ```bash
   # Search for recently modified files in the web root directory
   find /var/www/html/ -type f -mtime -3
   
   # Safely remove the malicious file
   rm /var/www/html/evil_shell.php
   ```
2. **Reset Credentials:** Force password resets for all active user accounts and system services (MySQL, Tomcat, root).
3. **Patch Vulnerabilities:** Update the vulnerable software components (e.g. upgrade PHP/Samba, or rewrite the vulnerable code to use parameterized queries).

---

### Phase 5: Recovery
Restore operations securely and verify system integrity before returning to production.

1. **Restore from Safe Backup:** If system files are compromised, restore clean configuration states from verified offsite backups.
2. **Verify Integrity:** Run file system integrity checkers (`AIDE`) to ensure no binaries have been replaced.
3. **Continuous Monitoring:** Implement close network monitoring and packet capturing for at least 72 hours to detect potential return attempts.

---

### Phase 6: Lessons Learned (Post-Incident Review)
* **Incident Analysis:** Convene the security team to review:
  * How did the incident occur? (e.g., exposed RFI vulnerability).
  * How quickly was it detected and contained?
* **Improve Security Policies:** Refine detection signatures, add additional security headers, patch outdated server services, and conduct follow-up training to address system vulnerabilities.
