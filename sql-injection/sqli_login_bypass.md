# SQL Injection - Login Bypass

## Lab Link
https://portswigger.net/web-security/sql-injection/lab-login-bypass

## Difficulty
Apprentice

## Vulnerability Type
SQL Injection (Authentication Bypass)

## Theory
SQL injection occurs when user input is concatenated directly into SQL queries without proper sanitization. When login forms build queries like:
```sql
SELECT * FROM users WHERE username = '[input]' AND password = '[input]'
```

An attacker can inject SQL syntax to bypass authentication. By using comment sequences or logical operators, they can modify query logic to return authenticated users without knowing passwords.

## Recon Process
1. Identified login form with username and password fields
2. Observed that form submission sends POST request to backend
3. Tested basic SQL injection vectors in username field
4. Discovered that SQL syntax is interpreted by backend

## Exploitation Steps

### Step 1: Test for SQLi Vulnerability
- Submit username: `admin' OR '1'='1`
- Submit password: (any value)
- Observe if login succeeds despite invalid password

### Step 2: Craft Authentication Bypass Payload
The key is to make the WHERE clause always true:
- Username: `administrator'--`
- Password: (any value, ignored due to comment)

Resulting query:
```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = '...'
```
The `--` comments out the password check, allowing login as administrator.

### Step 3: Complete Lab
- Submit login form with crafted payload
- Gain access to administrator account
- Lab solved

## Root Cause
The application fails to use parameterized queries or prepared statements. User input is directly concatenated into SQL queries, allowing attackers to inject arbitrary SQL syntax.

## Impact
- Authentication bypass
- Unauthorized account access
- Potential full account takeover
- Access to sensitive user data

## Mitigations
1. **Use Parameterized Queries/Prepared Statements**
   - Bind user input as parameters, not concatenated strings
   - Example: `PreparedStatement ps = connection.prepareStatement("SELECT * FROM users WHERE username = ? AND password = ?")`

2. **Input Validation**
   - Whitelist acceptable characters
   - Reject inputs with SQL keywords or dangerous characters

3. **Least Privilege**
   - Database users should have minimal required permissions
   - Don't run queries with admin-level database accounts

4. **Web Application Firewall (WAF)**
   - Detect and block SQL injection attempts
   - Monitor for suspicious query patterns

5. **Stored Procedures**
   - Use properly written stored procedures with parameterized inputs
   - Avoid dynamic SQL generation
