# Access Control - Insecure Direct Object Reference (IDOR)

## Lab Link
https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references

## Difficulty
Apprentice

## Vulnerability Type
Access Control - IDOR (Insecure Direct Object Reference)

## Theory
IDOR occurs when an application exposes object references (IDs, file names, user numbers) directly in the API/URL without proper authorization checks. Attackers can modify the reference to access other users' resources.

Example: `/my-account?user_id=123` - If attacker changes to user_id=124, they might access another user's account if no authorization is enforced.

## Recon Process
1. Identify URLs/parameters that reference objects: `/documents/document_id=1001`
2. Observe how object IDs are structured (sequential, guessable, etc.)
3. Test changing ID values to access other resources
4. Check if authorization checks exist before returning data
5. Confirm access to unauthorized resources

## Exploitation Steps

### Step 1: Identify Object Reference Pattern
- Browse as authenticated user
- View personal information/documents
- Note URL pattern: `/account?user_id=123` or `/download/file=5001`

### Step 2: Modify Object Reference
- Change parameter value: user_id=124, user_id=125, etc.
- Try predictable sequences or common IDs
- Observe if system returns another user's data

### Step 3: Exploit Access Control Gap
If no authorization check:
- Download other users' documents
- View confidential information
- Access admin functions

### Step 4: Extract Sensitive Data
```
Original: GET /download?fileid=5001 (own file)
Exploit:  GET /download?fileid=5000 (another user's file)
```

## Root Cause
- Application exposes object IDs in URLs/API
- No authorization check before returning object
- Assumption that users won't guess/modify IDs
- Missing access control validation

## Impact
- **Data Disclosure:** Accessing confidential data of other users
- **Privilege Escalation:** Accessing admin-only resources
- **Data Modification:** Editing other users' records
- **Compliance Violation:** Unauthorized access to PII

## Mitigations

### 1. **Indirect References**
```php
// Instead of: /download?file_id=5001
// Use: /download?file_token=abc123def456
// Map token to actual ID server-side
```

### 2. **Authorization Checks**
```python
user_file = UserFile.get(id=file_id)
if user_file.owner_id != current_user.id:
    raise UnauthorizedError()
```

### 3. **Parameterized Queries**
- Use parameterized database queries
- Filter results by current user

### 4. **Access Control Lists**
- Implement role-based access control (RBAC)
- Check user permissions before returning data

### 5. **API Design**
- Avoid exposing internal IDs
- Use UUIDs instead of sequential IDs
- Implement proper authentication/authorization

## Key Lesson
Never trust that users won't manipulate IDs in URLs/API parameters. Always verify authorization on the server-side.
