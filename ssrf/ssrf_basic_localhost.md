# SSRF - Basic Attack Against Local Server

## Lab Link
https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost

## Difficulty
Apprentice

## Vulnerability Type
Server-Side Request Forgery (SSRF)

## Theory
SSRF occurs when a web application makes HTTP requests on behalf of the user to URLs supplied by the attacker. The server performs the request from its own network, potentially accessing internal services not accessible from the internet.

Example: An image proxy that takes a URL parameter and fetches the image: `/proxy?url=http://attacker.com/image.jpg`

Attacker can redirect to internal services: `/proxy?url=http://localhost:8080/admin`

## Recon Process
1. Identify parameters that accept URLs (proxies, webhooks, image uploads)
2. Test with external URL to confirm functionality
3. Attempt localhost/internal IP addresses
4. Try different port numbers (8080, 3000, 5432)
5. Enumerate internal services accessible to server

## Exploitation Steps

### Step 1: Identify SSRF Vector
- Find feature that fetches remote content (image proxy, webhook, API calls)
- Intercept request with Burp Suite
- Note the parameter accepting URLs

### Step 2: Test with Localhost
Basic SSRF payload:
```
POST /product/stock HTTP/1.1
Content-Type: application/x-www-form-urlencoded

stockApi=http://localhost:8080/admin
```

### Step 3: Exploit Internal Services
If admin endpoint accessible:
- Access admin panel
- Extract sensitive information
- Modify application state

### Step 4: Complete Lab
- Access admin interface via SSRF
- Delete target user account
- Lab marked solved

## Root Cause
- Application fetches URLs without validating destination
- No whitelist of allowed domains
- No blocklist of dangerous domains (localhost, internal IPs)
- Server makes requests with full privileges

## Impact
- **Access to Internal Services:** Admin panels, databases
- **Port Scanning:** Enumerate internal infrastructure
- **Data Exfiltration:** Read internal files
- **Remote Code Execution:** If internal services are vulnerable

## Mitigations

### 1. **URL Whitelisting**
```python
ALLOWED_DOMAINS = ['cdn.example.com', 'api.partner.com']
if not url.startswith(tuple(ALLOWED_DOMAINS)):
    raise ValueError("URL not allowed")
```

### 2. **Block Internal IPs**
```python
from ipaddress import ip_address
TABOO_IPS = [
    ip_address('127.0.0.1'),      # Localhost
    ip_address('192.168.0.0/16'), # Private network
    ip_address('10.0.0.0/8'),     # Private network
]
```

### 3. **Disable Unused Protocols**
- Allow only HTTP/HTTPS
- Block file://, ftp://, gopher:// protocols

### 4. **Network Segmentation**
- Run app in isolated network
- Restrict outbound connections
- Monitor for SSRF attempts

### 5. **Timeout & Size Limits**
- Limit response size to prevent DoS
- Set request timeout

## Key Defense
Treat all user-supplied URLs as untrusted. Always validate and restrict destinations.
