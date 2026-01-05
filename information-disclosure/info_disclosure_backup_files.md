# Information Disclosure via Backup Files

## Lab Link
https://portswigger.net/web-security/information-disclosure/exploiting-backup-files

## Vulnerability Type
Information Disclosure (CWE-530)

## Difficulty
Easy

## Vulnerability Theory
Developers often create backup files for configuration, deployment, and maintenance purposes. These backups may include:
- Database configuration files
- API keys and secrets
- Source code with hardcoded credentials
- Database dumps
- Temporary files with sensitive data
- Old version control artifacts

If these files are accessible via the web server (through misconfiguration or being placed in the web root), attackers can retrieve them and extract sensitive information.

## Recon & Discovery Process
1. Identify common backup file extensions:
   - `.bak`, `.old`, `.backup`, `.tmp`, `.swp`, `.swo`
   - `.config.php`, `.env.bak`, `.sql.gz`
   - `.jar.original`, `.war.old`
2. Request common backup paths:
   - `config.php.bak`
   - `.env.bak`
   - `database.sql`
   - `backup.tar.gz`
3. Check for Git repositories, .git directories
4. Use search engines (Google dorking):
   - `site:target.com filetype:bak`
   - `site:target.com backup`
5. Scan common backup locations:
   - `/backups/`
   - `/downloads/`
   - `/temp/`
   - `/../`

## Step-by-Step Exploitation

### Step 1: Identify File Extensions
Based on the technology stack, try relevant backup extensions:
- PHP applications: `.php.bak`, `.php.old`
- Java: `.jar.original`, `.war.bak`
- Python: `.py.bak`, `.wsgi.old`
- Configuration: `.conf.bak`, `.config.bak`

### Step 2: Request Backup Files
**Request:**
```
GET /wp-config.php.bak HTTP/1.1
Host: target.com
```

**Response:**
```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
Content-Length: 1234

<?php
// ** MySQL settings ** //
define('DB_NAME', 'wordpress_db');
define('DB_USER', 'admin_user');
define('DB_PASSWORD', 'SuperSecret123!');
define('DB_HOST', 'localhost');
```

### Step 3: Extract Sensitive Information
From the backup file:
- Database credentials
- API endpoints
- Authentication tokens
- File paths and structure
- Third-party service credentials

### Step 4: Leverage Information for Further Attacks
- Use database credentials for direct database access
- Find API keys for third-party service compromise
- Discover internal paths for targeted attacks
- Identify additional systems and services

## Root Cause Analysis
Backup file disclosure typically occurs due to:

1. **Improper File Placement**: Backups created in web-accessible directories
2. **Weak Access Controls**: No restriction on serving backup files
3. **Default Behavior**: Web server configured to serve all files
4. **Lack of Cleanup**: Old backups never deleted
5. **Public Version Control**: .git directory exposed
6. **Misconfigured Web Server**: `.htaccess` or `web.config` not protecting backups

## Impact
**Severity: High**

Backup file disclosure can expose:
- Database credentials (leads to complete DB compromise)
- API keys (third-party service compromise)
- Encryption keys (data breach)
- Source code (code review vulnerabilities)
- Infrastructure details (targeted attacks)

## Code-Level Mitigation Examples

### Bad: Backups in Web Root
```bash
# Backup created in public directory
cp config.php /var/www/html/config.php.bak
```

### Good: Backups Outside Web Root
```bash
# Store backups outside public directory
cp config.php /var/backups/config.php.bak
chmod 600 /var/backups/config.php.bak
```

### Web Server Protection (.htaccess)
```apache
<FilesMatch "\.(bak|old|backup|tmp|swp|swo)$">
    Order Allow,Deny
    Deny from all
</FilesMatch>

# Prevent access to .git directory
<Directory ~ "\.git">
    Order allow,deny
    Deny from all
</Directory>
```

### Web Server Protection (Nginx)
```nginx
location ~ \.(bak|old|backup|tmp|swp|swo)$ {
    return 403;
}

location ~/.git {
    return 403;
}
```

## Prevention Strategies

1. **File Placement**
   - Store backups outside web root
   - Use `/var/backups/` or similar protected directory
   - Never commit backups to version control

2. **Web Server Hardening**
   - Configure server to deny backup file extensions
   - Block access to .git, .svn, .hg directories
   - Use .htaccess or web.config to restrict access

3. **Access Controls**
   - Restrict file permissions (mode 600 or 640)
   - Use authentication for backup access
   - Limit backup access to admin accounts only

4. **Development Practices**
   - Remove backups before deployment
   - Automate backup cleanup
   - Use proper .gitignore files
   - Never commit credentials to version control

5. **Monitoring**
   - Log access attempts to sensitive files
   - Monitor for requests to backup file extensions
   - Alert on unusual file access patterns

## Key Lessons & Takeaways

1. **Defense in Depth**: Multiple layers of protection (placement + access controls)
2. **Secure Defaults**: Block by default, allow only necessary files
3. **Lifecycle Management**: Clean up backups regularly
4. **Principle of Least Privilege**: Store credentials outside backups when possible
5. **Testing**: Regularly verify backup files are not accessible

## Related Labs
- Information Disclosure (error messages)
- Information Disclosure (git history)
- Information Disclosure (database errors)

---

**Difficulty Classification**: Easy - Direct file access
**Lab Type**: Information Gathering
**Time Estimate**: 5-10 minutes
