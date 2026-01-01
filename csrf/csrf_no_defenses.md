# CSRF - No Defenses

## Lab Link
https://portswigger.net/web-security/csrf/lab-no-defenses

## Difficulty
Apprentice

## Vulnerability Type
Cross-Site Request Forgery (CSRF)

## Theory
CSRF occurs when an authenticated user is tricked into performing unintended actions on a website. Attacker crafts a malicious request (via HTML form, image tag, JavaScript) that executes automatically when victim visits attacker's page while logged in.

Example: While victim is logged into bank.com, attacker sends them link to attacker.com which contains:
```html
<img src="https://bank.com/transfer?to=attacker&amount=1000">
```
Browser automatically sends authenticated request to bank.com

## Recon Process
1. Identify state-changing actions (transfer, delete, change settings)
2. Examine HTTP requests for CSRF tokens or protections
3. Check for SameSite cookie attribute
4. Determine if action requires CSRF token
5. Attempt creating request without token from different domain

## Exploitation Steps

### Step 1: Identify Vulnerable Action
- Find form that performs state change (account settings, password reset)
- Intercept with Burp
- Check if CSRF token present and validated

### Step 2: Create CSRF Exploit
```html
<form action="https://target.com/email/change" method="POST">
  <input type="hidden" name="email" value="attacker@evil.com">
  <input type="submit" value="Click here">
</form>
<script>document.forms[0].submit();</script>
```

### Step 3: Test the Exploit
- Save as HTML file
- Access while logged into target app
- Verify state change executed without user consent

## Root Cause
- No CSRF token validation
- No SameSite cookie protection
- No referer/origin header checks
- Assumption that browser same-origin policy prevents attacks

## Impact
- **Account Takeover:** Change password, email, 2FA settings
- **Fraudulent Transactions:** Transfer money, place orders
- **Privilege Changes:** Add/remove admin users
- **Data Modification:** Update sensitive user information

## Mitigations

### 1. **CSRF Tokens**
```html
<form method="POST" action="/change-email">
  <input type="hidden" name="csrf-token" value="abc123...">
  <input type="email" name="email">
  <input type="submit">
</form>
```
Server validates token on request.

### 2. **SameSite Cookies**
```
Set-Cookie: session=abc123; SameSite=Strict
```
Prevents cookies being sent in cross-site requests.

### 3. **Double-Submit Pattern**
- Store token in cookie and request body
- Verify they match

### 4. **Custom Headers**
```javascript
// Always include custom header that cannot be set via HTML form
fetch('/api/update', {
  method: 'POST',
  headers: {'X-Custom-Header': token}
})
```

## Key Defense
Implement CSRF tokens + SameSite cookies for defense in depth.
