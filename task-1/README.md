# Task 1: Foundations of Cybersecurity

## 🎯 Objective
To establish a strong foundation in cybersecurity principles, understand threat vectors, construct a professional sandbox hacking laboratory, master Linux command-line basics, and understand cryptographic applications.

---

## 🛠️ Laboratory Network Architecture
To conduct security scanning and exploitation safely, a private host-only virtual network was built.

### Network Diagram
```
              [ Host Machine (Windows 11) ]
                           │
             (VirtualBox Host-Only Adapter)
                           │
       ┌───────────────────┴───────────────────┐
       ▼ (192.168.56.0/24 Subnet)              ▼
┌──────────────┐                       ┌──────────────┐
│  Attacker VM │                       │  Target VM   │
│ (Kali Linux) │ ──[Private Network]── │(Metasploitable2)
│192.168.56.101│                       │192.168.56.102│
└──────────────┘                       └──────────────┘
```

### Setup Details
1. **Hypervisor:** Oracle VM VirtualBox.
2. **Attacker:** Kali Linux (2024.x release) configured with 2 vCPUs, 2GB RAM, and a Host-Only Network Adapter.
3. **Target 1:** Metasploitable2 VM configured with 1 vCPU, 512MB RAM, and bound to the same Host-Only network.
4. **Target 2:** DVWA (Damn Vulnerable Web Application) running inside an Apache web server.

---

## 🔐 Cryptography Hands-On (OpenSSL)

Here is a summary of the cryptographic commands run to demonstrate hashing, symmetric encryption, and asymmetric keys using OpenSSL.

### 1. File Hashing (Integrity verification)
To generate SHA-256 hashes of text files to verify integrity:
```bash
# Create a test message
echo "Confidential internship task data" > secret.txt

# Generate SHA-256 hash
openssl dgst -sha256 secret.txt
# Output: SHA2-256(secret.txt)= 896f6ae9b51fa16db32bf1abf67c33b7ba82c875d045d9fa9f0bd5a28adcf25a
```

### 2. Symmetric Encryption (AES-256-CBC)
Encrypting and decrypting files using a shared password:
```bash
# Encrypt the file using AES-256-CBC with salt
openssl enc -aes-256-cbc -salt -in secret.txt -out secret.enc -pbkdf2

# Decrypt the encrypted file
openssl enc -aes-256-cbc -d -in secret.enc -out secret_decrypted.txt -pbkdf2
```

### 3. Asymmetric Key Generation (RSA)
Generating an RSA private-public keypair:
```bash
# Generate a 2048-bit RSA private key
openssl genrsa -out private_key.pem 2048

# Extract the public key from the private key
openssl rsa -in private_key.pem -pubout -out public_key.pem
```

---

## 🧰 Security Tool Familiarization

The following tools were initialized and tested on the Host-Only network to ensure proper functionality:
* **Wireshark:** Configured to monitor interfaces and capture packet traffic on `eth0` (Host-Only card).
* **Nmap:** Verified scanning parameters using simple ping sweeps (`nmap -sn 192.168.56.0/24`).
* **Burp Suite:** Set up as an HTTP intercepting proxy pointing browser traffic from Kali Firefox to port `8080`.
* **Netcat:** Tested communication sockets, setting up listeners (`nc -lvnp 4444`) and connecting streams.

---

## 📂 Sub-deliverables
* 📜 [Linux & Networking Cheat-Sheet](./linux_cheatsheet.md)

---

## 🎥 Task 1 Video Demonstration
> * **LinkedIn Video Walkthrough:** `https://www.linkedin.com/posts/kalyan-katta-6b491338b_cybersecurity-ethicalhacking-kalilinux-ugcPost-7485335576527769600-K4uW/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGAQxSgBWlNdbkRaK_lHTiAJO0x0AIxoNAo[Link to LinkedIn Video]`

