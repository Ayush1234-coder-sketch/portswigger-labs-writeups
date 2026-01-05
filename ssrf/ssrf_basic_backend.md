# SSRF - Basic Attack Against Backend System

## Lab Link
https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system

## Difficulty
Apprentice

## Vulnerability Type
Server-Side Request Forgery (SSRF) - Backend System Access

## Theory
SSRF vulnerability allowing access to internal backend systems on private networks. Server makes requests to internal IPs/services that aren't publicly accessible.

Example:
- Frontend: `stockapi.example.com` (public)
- Backend: `192.168.1.10` (internal, not accessible from internet)
- Vulnerability: SSRF lets attacker query backend through frontend

## Exploitation Steps

### Step 1: Identify SSRF Point
Find API parameter accepting URLs (typically stock checking endpoints)

### Step 2: Scan Internal IP Range
```
Payload: http://192.168.1.1
Payload: http://192.168.1.10
Payload: http://10.0.0.1
```

### Step 3: Enumerate Internal Services
Test different ports:
```
http://192.168.1.10:8080
http://192.168.1.10:3000
http://192.168.1.10:5432
```

### Step 4: Extract Data
Once internal service found, interact with it:
```
GET /admin (admin panel)
GET /api/users (data extraction)
```

## Root Cause
- No URL validation
- No internal IP range blocking
- Trust in network boundary
- Direct URL usage in server requests

## Impact
- Access internal services
- Port scanning
- Data exfiltration
- RCE on internal systems

## Mitigations

### 1. **Block Internal IP Ranges**
```python
from ipaddress import ip_address, ip_network

BLOCK_RANGES = [
    ip_network('127.0.0.0/8'),      # Loopback
    ip_network('10.0.0.0/8'),       # Private
    ip_network('172.16.0.0/12'),    # Private
    ip_network('192.168.0.0/16'),   # Private
    ip_network('169.254.0.0/16'),   # Link-local
]

def is_internal(url):
    hostname = urllib.parse.urlparse(url).hostname
    ip = ip_address(hostname)
    return any(ip in range for range in BLOCK_RANGES)
```

### 2. **URL Whitelist**
```python
ALLOWED_DOMAINS = ['api.trusted-partner.com', 'data.example.com']
```

### 3. **Separate Networks**
- Run applications in isolated networks
- Use network segmentation

### 4. **Disable Unused Protocols**
- Only allow HTTP/HTTPS
- Block file://, gopher://, etc.

## Key Lesson
Always validate SSRF payloads against internal IP ranges and private networks.
