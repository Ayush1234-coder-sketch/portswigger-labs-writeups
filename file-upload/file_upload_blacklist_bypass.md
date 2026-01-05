# File Upload - Blacklist Bypass

## Lab Link
https://portswigger.net/web-security/file-upload/lab-file-upload-blacklist-bypass

## Vulnerability Type
Unrestricted File Upload

## Difficulty
Easy

## Theory
File upload filters using blacklists can be bypassed:
- Double extensions: shell.php.jpg
- Null byte: shell.php%00.jpg
- Case variation: shell.pHP
- Alternative extensions: shell.php3, shell.phtml, shell.php7
- Polyglot files (JPEG + PHP combined)

## Exploitation

**Blocked: .php, .exe, .sh**

**Bypass attempts:**
```
1. shell.php.jpg (if server processes .php first)
2. shell.php5 (alternative PHP extension)
3. shell.phtml (alternate)
4. shell.phar (PHP archive)
```

## Prevention
- Whitelist safe extensions only
- Validate file content/magic bytes
- Store uploads outside web root
- Use Content-Disposition header
- Randomize filenames

---

**Difficulty**: Easy
**Time**: 10 minutes
