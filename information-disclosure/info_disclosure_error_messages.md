# Information Disclosure via Error Messages

## Lab Link
https://portswigger.net/web-security/information-disclosure/exploiting-error-messages

## Vulnerability Type
Information Disclosure (CWE-209)

## Difficulty
Easy

## Vulnerability Theory
Applications often generate detailed error messages when things go wrong. These error messages may contain sensitive information such as:
- Stack traces revealing the web framework and version
- Database error messages exposing table/column names
- File paths revealing server structure
- SQL queries in error logs
- Internal IP addresses or system details
- API endpoints and internal mechanisms

While detailed error messages are useful for developers during debugging, exposing them in production environments provides attackers with valuable reconnaissance information.

## Recon & Discovery Process
1. Interact with the application normally
2. Trigger errors by:
   - Entering invalid input in forms
   - Requesting non-existent resources
   - Attempting SQL injection payloads
   - Providing malformed data types
3. Observe the error messages returned
4. Look for tech stack hints (Apache, Nginx, PHP version, Django, etc.)
5. Search error messages on Google to find known vulnerabilities in that version
6. Use the disclosed information to identify other attack vectors

## Step-by-Step Exploitation

### Step 1: Identify Error-Prone Endpoints
- Browse the application and note endpoints that might generate errors
- Common locations: login, search, file upload, checkout

### Step 2: Trigger Error Conditions
**Request:**
```
GET /product/nonexistent HTTP/1.1
Host: target.com
```

**Response with Information Leakage:**
```
HTTP/1.1 500 Internal Server Error
Content-Type: text/html

<html>
<body>
Fatal error: Call to undefined function in /var/www/html/products.php on line 42
</body>
</html>
```

### Step 3: Analyze Disclosed Information
From the error, we learn:
- Web root: `/var/www/html/`
- File structure and naming: `products.php`
- Server OS: Linux (path format)
- Possible PHP version: Older (function name format)

### Step 4: Search for Vulnerability Information
Use the tech stack info to search for known CVEs:
- Search "Apache 2.4.41 vulnerability"
- Look up that PHP version's CVEs
- Check framework-specific issues

### Step 5: Escalate to Further Attacks
The information disclosure may lead to:
- Finding versions with known RCE exploits
- Identifying deprecated libraries
- Discovering exposed debug endpoints
- Finding predictable paths for other resources

## Root Cause Analysis
Error handling is not properly secured in production environments. Common causes:

1. **Debug Mode Enabled**: Applications left in debug mode expose detailed stack traces
2. **Default Error Handlers**: Framework default error pages not overridden
3. **Verbose Logging**: Error logs accessible to users
4. **Lack of Sanitization**: Error messages not filtered before display
5. **Version Disclosure**: Server headers and framework information leaked

## Impact
**Severity: Medium**

While not directly exploitable, information disclosure:
- Enables reconnaissance for targeted attacks
- Reveals exploitable versions of software
- Reduces time needed for attacker to understand the system
- May expose data structure and business logic
- Helps attacker identify the technology stack

## Code-Level Mitigation Examples

### Bad: Exposing Stack Traces
```python
@app.route('/api/data')
def get_data():
    try:
        return fetch_data()  # No error handling
    except Exception as e:
        return str(e), 500  # Exposes full traceback
```

### Good: Custom Error Pages
```python
@app.route('/api/data')
def get_data():
    try:
        return fetch_data()
    except DatabaseError as e:
        logger.error(f"DB Error: {e}", extra={'user': request.remote_addr})
        return {"error": "An error occurred"}, 500

@app.errorhandler(500)
def handle_error(e):
    logger.error(f"Server error: {e}")
    return {"error": "Internal server error"}, 500
```

### Disable Debug Mode
```python
# Flask example
app.config['DEBUG'] = False
app.config['PROPAGATE_EXCEPTIONS'] = False
```

### Filter Server Headers
```python
@app.after_request
def hide_server_info(response):
    response.headers.pop('Server', None)
    response.headers.pop('X-Powered-By', None)
    return response
```

## Prevention Strategies

1. **Production Configuration**
   - Disable debug mode in production
   - Set logging to appropriate levels (ERROR only in production)
   - Remove unnecessary server headers

2. **Custom Error Pages**
   - Override default error handlers
   - Return generic error messages to users
   - Log detailed errors server-side only

3. **Input Validation**
   - Validate and sanitize all inputs
   - Catch specific exceptions
   - Handle errors gracefully

4. **Server Hardening**
   - Hide server version information
   - Remove default error pages
   - Configure proper error logging

5. **Testing**
   - Regularly test error handling
   - Verify no sensitive info in responses
   - Automated security scanning

## Key Lessons & Takeaways

1. **Defense in Depth**: Never rely on obscurity; implement actual security controls
2. **Principle of Least Privilege**: Users should only see necessary information
3. **Logging vs. Exposure**: Log everything, but expose nothing
4. **Configuration Matters**: Same code behaves differently in dev vs. production
5. **Reconnaissance is Key**: Attackers use information disclosure to plan further attacks

## Related Labs
- Information Disclosure (backup files)
- Information Disclosure (database error messages)
- Information Disclosure (version history in Git)

---

**Difficulty Classification**: Easy - High-value recon, low exploitation complexity
**Lab Type**: Information Gathering
**Time Estimate**: 10-15 minutes
