# SQL Injection in DVWA (Low & Medium Security Levels)

**Author:** Garba Abdulsomad  
**Date:** 5 September 2026  
**Target:** Damn Vulnerable Web Application (DVWA)  
**Vulnerability Type:** SQL Injection (CWE-89)  
**Severity:** High  
**CVSS v3.1 (Estimated):** 7.5 (High)  
**Tools Used:** Burp Suite Community Edition, Firefox, Manual Testing  

---

## Executive Summary

A SQL Injection vulnerability was discovered in the User ID parameter of the DVWA SQL Injection page on both Low and Medium security levels. User input is concatenated directly into SQL queries, allowing an attacker to manipulate the query logic, extract sensitive database information, and potentially perform unauthorized actions on the backend database.

---

## Vulnerability Details

| Field                  | Value                                      |
|------------------------|--------------------------------------------|
| Affected Component     | SQL Injection page – User ID parameter     |
| Security Levels        | Low and Medium                             |
| Vulnerability Type     | SQL Injection                              |
| Authentication Required| Yes                                        |

---

## Steps to Reproduce

### Low Security Level

1. Log in to DVWA and set the security level to **Low**.
2. Navigate to the **SQL Injection** page.
3. Enter the following payload in the User ID field:

   `1' AND 1=1#`

4. Submit the request. The application returns the admin user record, confirming the parameter is injectable.
5. Confirm the query returns 2 columns.
6. Extract table names using:

   `1' UNION SELECT 1, table_name FROM information_schema.tables#`

7. Target the `dvwa` database with:

   `1' UNION SELECT 1, table_name FROM information_schema.tables WHERE table_schema = 'dvwa'#`

   This successfully returned the tables `admin`, `guestbook`, and `users`.

### Medium Security Level

1. The payload containing a single quote fails:

   `1' AND 1=1#`

2. Removing the single quote allows the payload to work:

   `1 AND 1=1#`

3. Standard UNION-based queries with quotes fail due to escaping.
4. Bypassing the filter by removing quotes and encoding the database name `dvwa` as hexadecimal (`0x64767761`) successfully extracts the table names (`admin`, `guestbook`, and `users`).

---

## Impact

Successful exploitation of this vulnerability could allow an attacker to:

- Extract sensitive information from the database (including user credentials and application data)
- Bypass authentication controls
- Modify or delete database records
- Potentially escalate the attack to compromise the database server

---

## Root Cause Analysis

The application builds SQL queries by directly concatenating user-controlled input into the query string.  

At the Medium security level, DVWA uses `mysql_real_escape_string()` to escape special characters. While this offers limited protection against basic quoted injection techniques, the input is still concatenated into the query. This leaves the application vulnerable to alternative techniques such as quote removal and hexadecimal encoding.

---

## Remediation Recommendations

1. **Use Parameterized Queries (Prepared Statements)** – This is the strongest and recommended defense.
2. **Never concatenate user input** directly into SQL queries.
3. **Implement strict input validation** (for example, only allow integers in the User ID field).
4. **Apply the principle of least privilege** to the database account used by the application.

---

## Skills Demonstrated

- Manual SQL Injection testing and exploitation
- Bypassing basic input escaping mechanisms
- UNION-based data extraction
- Testing across different security levels
- Clear technical documentation of findings, impact, and remediation

---

## References

- OWASP SQL Injection
- CWE-89: Improper Neutralization of Special Elements used in an SQL Command
- PortSwigger Web Security Academy – SQL Injection
- DVWA Official Documentation
