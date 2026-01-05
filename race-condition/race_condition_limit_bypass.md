# Race Condition - Limit Bypass

## Lab Link
https://portswigger.net/web-security/race-conditions/lab-race-conditions-limit-overrun

## Vulnerability Type
Race Condition

## Difficulty
Medium-Hard

## Theory
Rate limiting and account lockouts are vulnerable to race conditions:
- Multiple requests processed before rate limit updated
- Brute force in parallel windows
- Account unlock race conditions
- Concurrent transaction processing

## Exploitation

**Vulnerable flow:**
- Lockout after 5 failed attempts
- Check happens in single thread
- Update happens after check

**Attack:**
Send 20 authentication requests in parallel window:
```
Thread 1: POST /login (user=admin, pass=wrong1) - checked count=0, passed
Thread 2: POST /login (user=admin, pass=wrong2) - checked count=0, passed
Thread 3: POST /login (user=admin, pass=wrong3) - checked count=0, passed
...
```

All requests pass the check before ANY updates the counter!

## Tools
- Burp Suite Intruder (serial tab)
- Python with threading
- Turbo Intruder

## Prevention
- Use atomic database operations
- Implement proper locking mechanisms
- Use rate limiting middleware
- Time-based rather than count-based limits

---

**Difficulty**: Medium-Hard
**Tools**: Burp Intruder, Turbo Intruder
**Time**: 20-30 minutes
