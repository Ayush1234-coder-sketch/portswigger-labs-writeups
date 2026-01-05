# Deserialization Object Modification

## Lab Link
https://portswigger.net/web-security/deserialization/exploiting-deserialization-vulnerabilities

## Vulnerability Type
Deserialization / Remote Code Execution (RCE)

## Difficulty
Medium

## Vulnerability Theory
Serialization converts objects to a stream of bytes for storage/transmission. Deserialization reverses this. If an application deserializes untrusted data without validation:
- Object properties can be modified
- Sensitive operations can be triggered
- RCE can occur with gadget chains
- Privilege escalation possible

## Recon & Discovery
1. Look for serialized data in:
   - Cookies and session tokens
   - HTTP request parameters
   - API responses
   - Cache headers
2. Identify serialization format:
   - Java: `ac ed 00 05` magic bytes (base64: `rO0AB`)
   - PHP: Text format or `O:classname`
   - Python: Pickle format
3. Decode and analyze object properties
4. Identify gadget chains for RCE

## Step-by-Step Exploitation

### Step 1: Identify Serialized Data
**Request with session cookie:**
```
GET / HTTP/1.1
Host: target.com
Cookie: session=rO0ABXNyABNqYXZhLnV0aWwuQXJyYXlMaXN0eHYtC0...
```

### Step 2: Decode Serialized Object
- Base64 decode the cookie
- Identify Java serialization magic bytes
- Use tools like ysoserial to generate payloads

### Step 3: Modify Object Properties
**Payload (user object with admin=true):**
```
rO0ABXNyABBjb20udXNlci5Vc2VyT2JqZWN0AAAA...
(Modified to set admin flag)
```

### Step 4: Execute RCE
Use ysoserial to generate gadget chain payload:
```bash
ysoserial CommonsCollections5 'calc.exe' | base64
```

## Root Cause
- Deserializing untrusted data
- No input validation
- Using unsafe deserialization methods
- Vulnerable gadget chains available

## Prevention
1. **Never Deserialize Untrusted Data**
   ```java
   // BAD
   ObjectInputStream ois = new ObjectInputStream(inputStream);
   Object obj = ois.readObject();
   ```

2. **Use Safe Alternatives**:
   ```java
   // GOOD - Use JSON instead
   ObjectMapper mapper = new ObjectMapper();
   User user = mapper.readValue(jsonString, User.class);
   ```

3. **Input Validation**:
   - Whitelist allowed classes
   - Check object types before deserialization

## Key Lessons
- Serialized data is code, not data
- Use JSON/XML instead of native serialization
- Implement object whitelisting
- Keep libraries updated (gadget chains patched)

---

**Difficulty**: Medium
**Tools**: ysoserial, Burp Suite
**Time Estimate**: 20-30 minutes
