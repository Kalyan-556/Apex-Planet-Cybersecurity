# Web Application Vulnerability Mitigation Guide

This guide details developer-focused secure coding practices in PHP to protect web applications from SQL Injection, Cross-Site Scripting (XSS), Cross-Site Request Forgery (CSRF), and File Inclusion.

---

## 💾 1. Mitigating SQL Injection (SQLi)

### Vulnerable Code (PHP/MySQL)
Directly concatenating user inputs into an SQL query creates a vulnerable query string.
```php
<?php
// VULNERABLE: Direct string concatenation
$id = $_GET['id'];
$query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id'";
$result = mysqli_query($GLOBALS["___mysqli_ston"], $query);
?>
```

### Secure Code (PDO with Prepared Statements)
Prepared statements separate SQL syntax from parameters. The database treats parameters strictly as data, never as executable code.
```php
<?php
// SECURE: PDO Prepared Statements
$db = new PDO('mysql:host=localhost;dbname=dvwa', 'dbuser', 'dbpassword');

$id = $_GET['id'];
$stmt = $db->prepare('SELECT first_name, last_name FROM users WHERE user_id = :id');
$stmt->execute(['id' => $id]);
$user = $stmt->fetch();
?>
```

---

## 🎨 2. Mitigating Cross-Site Scripting (XSS)

### Vulnerable Code
Reflecting raw user input allows the browser to interpret and execute scripts.
```php
<?php
// VULNERABLE: Printing unescaped user input
echo "Hello, " . $_GET['name'];
?>
```

### Secure Code (Output Encoding)
Escaping special characters prevents the browser from executing inputs as code.
```php
<?php
// SECURE: HTML Entity Encoding
$name = $_GET['name'];
$safe_name = htmlspecialchars($name, ENT_QUOTES, 'UTF-8');
echo "Hello, " . $safe_name;
?>
```
* **Defense-in-Depth:** Implement Content Security Policy (CSP) response headers to block inline scripts entirely:
  ```http
  Content-Security-Policy: default-src 'self'; script-src 'self';
  ```

---

## 🔑 3. Mitigating Cross-Site Request Forgery (CSRF)

### Vulnerable Code
Allowing actions based purely on active cookies, without custom session tokens, lets secondary sites trigger malicious operations.
```php
<?php
// VULNERABLE: Modifying passwords without token verification
if (isset($_GET['Change'])) {
    $password_new = $_GET['password_new'];
    // Directly updating database...
}
?>
```

### Secure Code (Anti-CSRF Tokens)
Validating a random, cryptographically secure token stored in the session and compared against forms prevents arbitrary third-party submissions.
```php
<?php
// SECURE: Generating and verifying Anti-CSRF token
session_start();

// 1. Generate token for forms
if (empty($_SESSION['csrf_token'])) {
    $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
}

// 2. Validate token on POST submission
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    if (!hash_equals($_SESSION['csrf_token'], $_POST['csrf_token'])) {
        die("CSRF Token validation failed.");
    }
    // Proceed with password modification...
}
?>
```
* **Form Implementation:** Include the token in a hidden field:
  ```html
  <input type="hidden" name="csrf_token" value="<?php echo $_SESSION['csrf_token']; ?>">
  ```

---

## 📂 4. Mitigating Local File Inclusion (LFI)

### Vulnerable Code
Directly using parameters to reference local files allows directory traversal attacks (`../`).
```php
<?php
// VULNERABLE: Including files directly from user parameters
$file = $_GET['page'];
include($file);
?>
```

### Secure Code (Whitelisting & Basename)
Strict input validations prevent traversal or execution of unauthorized system scripts.
```php
<?php
// SECURE Option A: Strict Whitelist Verification
$allowed_pages = [
    'home.php' => 'home.php',
    'contact.php' => 'contact.php',
    'about.php' => 'about.php'
];

$page = $_GET['page'];
if (!array_key_exists($page, $allowed_pages)) {
    die("Access denied.");
}
include($allowed_pages[$page]);

// SECURE Option B: Using basename() to strip paths
$safe_file = basename($_GET['page']); // Removes directory directories (e.g. "../../etc/passwd" becomes "passwd")
include("/var/www/html/pages/" . $safe_file);
?>
```
