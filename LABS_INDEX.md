# PortSwigger Web Security Academy - Complete Labs Index

**Last Updated:** January 1, 2026  
**Total Solved Labs:** 78  
**Status:** Batch writeup in progress

---

## Directory Structure

```
portswigger-labs-writeups/
├── sql-injection/
├── cross-site-scripting/
├── csrf/
├── ssrf/
├── access-control/
├── os-command-injection/
├── insecure-deserialization/
├── information-disclosure/
├── file-upload/
├── graphql-api/
├── api-testing/
└── README.md
```

---

## SQL Injection (3 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| SQL injection vulnerability allowing login bypass | Apprentice | ✅ Completed | `sqli_login_bypass.md` |
| SQL injection UNION attack, retrieving data from other tables | Practitioner | 📝 Pending | `sqli_union_retrieve_data.md` |
| Blind SQL injection with conditional responses | Practitioner | 📝 Pending | `sqli_blind_conditional.md` |

---

## Cross-Site Scripting (15 labs)

### Reflected XSS
| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Reflected XSS into HTML context with nothing encoded | Apprentice | 📝 Pending | `xss_reflected_html_nothing_encoded.md` |
| Reflected XSS into attribute with angle brackets HTML-encoded | Apprentice | 📝 Pending | `xss_reflected_attribute_angle_brackets.md` |
| Reflected XSS into a JavaScript string with angle brackets HTML encoded | Apprentice | 📝 Pending | `xss_reflected_js_string_angle_brackets.md` |

### Stored XSS
| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Stored XSS into HTML context with nothing encoded | Apprentice | 📝 Pending | `xss_stored_html_nothing_encoded.md` |
| Stored XSS into anchor href attribute with double quotes HTML-encoded | Apprentice | 📝 Pending | `xss_stored_href_double_quotes.md` |

### DOM-Based XSS
| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| DOM XSS in document.write sink using source location.search | Apprentice | 📝 Pending | `xss_dom_document_write.md` |
| DOM XSS in innerHTML sink using source location.search | Apprentice | 📝 Pending | `xss_dom_innerhtml.md` |
| DOM XSS in jQuery anchor href attribute sink using location.search source | Apprentice | 📝 Pending | `xss_dom_jquery_href.md` |
| DOM XSS in jQuery selector sink using a hashchange event | Apprentice | 📝 Pending | `xss_dom_jquery_selector.md` |
| DOM XSS in document.write sink using source location.search inside a select element | Practitioner | 📝 Pending | `xss_dom_document_write_select.md` |
| DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded | Practitioner | 📝 Pending | `xss_dom_angularjs.md` |
| Reflected DOM XSS | Practitioner | 📝 Pending | `xss_reflected_dom.md` |
| Stored DOM XSS | Practitioner | 📝 Pending | `xss_stored_dom.md` |
| Reflected XSS into HTML context with most tags and attributes blocked | Practitioner | 📝 Pending | `xss_reflected_tags_blocked.md` |

---

## CSRF (1 lab)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| CSRF vulnerability with no defenses | Apprentice | 📝 Pending | `csrf_no_defenses.md` |

---

## Server-Side Request Forgery - SSRF (7 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Basic SSRF against the local server | Apprentice | 📝 Pending | `ssrf_basic_localhost.md` |
| Basic SSRF against another back-end system | Apprentice | 📝 Pending | `ssrf_basic_backend.md` |
| Blind SSRF with out-of-band detection | Practitioner | 📝 Pending | `ssrf_blind_oob.md` |
| SSRF with blacklist-based input filter | Practitioner | 📝 Pending | `ssrf_blacklist_filter.md` |
| SSRF with filter bypass via open redirection vulnerability | Practitioner | 📝 Pending | `ssrf_filter_bypass_redirect.md` |
| Blind SSRF with Shellshock exploitation | Expert | 📝 Pending | `ssrf_blind_shellshock.md` |
| SSRF with whitelist-based input filter | Expert | 📝 Pending | `ssrf_whitelist_filter.md` |

---

## OS Command Injection (5 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| OS command injection, simple case | Apprentice | 📝 Pending | `cmd_injection_simple.md` |
| Blind OS command injection with time delays | Practitioner | 📝 Pending | `cmd_injection_blind_time_delay.md` |
| Blind OS command injection with output redirection | Practitioner | 📝 Pending | `cmd_injection_blind_output_redirect.md` |
| Blind OS command injection with out-of-band interaction | Practitioner | 📝 Pending | `cmd_injection_blind_oob.md` |
| Blind OS command injection with out-of-band data exfiltration | Practitioner | 📝 Pending | `cmd_injection_blind_oob_exfil.md` |

---

## Access Control Vulnerabilities (13 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Unprotected admin functionality | Apprentice | 📝 Pending | `access_control_unprotected_admin.md` |
| Unprotected admin functionality with unpredictable URL | Apprentice | 📝 Pending | `access_control_unpredictable_url.md` |
| User role controlled by request parameter | Apprentice | 📝 Pending | `access_control_role_parameter.md` |
| User role can be modified in user profile | Apprentice | 📝 Pending | `access_control_role_modification.md` |
| User ID controlled by request parameter | Apprentice | 📝 Pending | `access_control_user_id_parameter.md` |
| User ID controlled by request parameter with unpredictable user IDs | Apprentice | 📝 Pending | `access_control_unpredictable_ids.md` |
| User ID controlled by request parameter with data leakage in redirect | Apprentice | 📝 Pending | `access_control_data_leakage_redirect.md` |
| User ID controlled by request parameter with password disclosure | Apprentice | 📝 Pending | `access_control_password_disclosure.md` |
| Insecure direct object references | Apprentice | 📝 Pending | `access_control_idor.md` |
| URL-based access control can be circumvented | Practitioner | 📝 Pending | `access_control_url_circumvent.md` |
| Method-based access control can be circumvented | Practitioner | 📝 Pending | `access_control_method_circumvent.md` |
| Multi-step process with no access control on one step | Practitioner | 📝 Pending | `access_control_multistep.md` |
| Referer-based access control | Practitioner | 📝 Pending | `access_control_referer.md` |

---

## Insecure Deserialization (2 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Modifying serialized objects | Apprentice | 📝 Pending | `deserialization_modify_objects.md` |
| Modifying serialized data types | Practitioner | 📝 Pending | `deserialization_modify_types.md` |

---

## Information Disclosure (3 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Information disclosure in error messages | Apprentice | 📝 Pending | `info_disclosure_error_messages.md` |
| Source code disclosure via backup files | Apprentice | 📝 Pending | `info_disclosure_backup_files.md` |

---

## File Upload Vulnerabilities (3 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Web shell upload via Content-Type restriction bypass | Apprentice | 📝 Pending | `file_upload_content_type_bypass.md` |
| Web shell upload via extension blacklist bypass | Practitioner | 📝 Pending | `file_upload_blacklist_bypass.md` |
| Remote code execution via polyglot web shell upload | Practitioner | 📝 Pending | `file_upload_polyglot.md` |

---

## GraphQL API Vulnerabilities (3 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Accessing private GraphQL posts | Apprentice | 📝 Pending | `graphql_private_posts.md` |
| Accidental exposure of private GraphQL fields | Practitioner | 📝 Pending | `graphql_private_fields.md` |
| Finding a hidden GraphQL endpoint | Practitioner | 📝 Pending | `graphql_hidden_endpoint.md` |

---

## API Testing (5 labs)

| Lab Name | Difficulty | Status | File |
|-----------|-----------|--------|------|
| Exploiting an API endpoint using documentation | Apprentice | 📝 Pending | `api_exploit_documentation.md` |
| Exploiting server-side parameter pollution in a query string | Practitioner | 📝 Pending | `api_sspp_query_string.md` |
| Finding and exploiting an unused API endpoint | Practitioner | 📝 Pending | `api_unused_endpoint.md` |
| Exploiting a mass assignment vulnerability | Practitioner | 📝 Pending | `api_mass_assignment.md` |
| Exploiting server-side parameter pollution in a REST URL | Expert | 📝 Pending | `api_sspp_rest_url.md` |

---

## Statistics

- **Total Labs:** 78
- **Completed Writeups:** 1 (✅ SQL injection - Login Bypass)
- **Pending Writeups:** 77
- **Apprentice Labs:** 28
- **Practitioner Labs:** 45
- **Expert Labs:** 5

---

## How to Use This Repository

1. Each lab has its own markdown file organized by vulnerability category
2. Files follow the naming convention: `category_lab-description.md`
3. Each writeup includes:
   - Lab link to PortSwigger Academy
   - Difficulty level
   - Vulnerability type
   - Theory and explanation
   - Recon process
   - Step-by-step exploitation
   - Root cause analysis
   - Impact assessment
   - Mitigation strategies

---

## Next Steps

Remaining writeups will be generated and added in batches. Priority order:
1. XSS labs (high frequency vulnerability)
2. Access Control labs (critical security issue)
3. SSRF & Command Injection labs
4. API & GraphQL labs
5. Remaining vulnerabilities

---

**Contributing:** This repository is for personal learning and professional development in web security testing.
