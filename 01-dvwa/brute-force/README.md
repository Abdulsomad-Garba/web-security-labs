# Brute Force Attack on DVWA Login Page

**Author:** Garba Abdulsomad  
**Date:** 6 September 2026  
**Target:** Damn Vulnerable Web Application (DVWA)  
**Vulnerability Type:** Broken Authentication – Brute Force (CWE-307)  
**Severity:** High  
**Tools Used:** Burp Suite Community Edition (Intruder), Firefox  

---

## Executive Summary

A brute force vulnerability was identified on the DVWA login page. The application fails to implement adequate protections against repeated login attempts, such as account lockout, rate limiting, or multi-factor authentication. This allows an attacker to systematically test multiple password combinations against a valid username until the correct credentials are found.

---

## Steps to Reproduce

1. Log in to DVWA and navigate to the **Brute Force** page.
2. Intercept the login request using Burp Suite.
3. Send the intercepted request to **Intruder**.
4. Clear existing payload positions and set the password parameter as the only payload position.
5. Set the attack type to **Sniper**.
6. In the Payloads tab, select **Simple list** as the payload type.
7. Load a wordlist (e.g., `fsattack.txt` or any common passwords list).
8. Start the attack.
9. Analyze the results by comparing **response length**. A successful login typically returns a different response length compared to failed attempts.

---

## Impact

Successful exploitation of this vulnerability could allow an attacker to:

- Take over user accounts
- Gain unauthorized access to the application
- Modify or delete user information
- Potentially escalate privileges within the application

---

## Root Cause Analysis

The application lacks several critical authentication security controls:

- **No rate limiting**: Unlimited login attempts are accepted without any throttling.
- **No account lockout**: Repeated failed login attempts do not lock or flag the account.
- **No CAPTCHA** after multiple failed attempts.
- **No multi-factor authentication (MFA)**.
- Weak or predictable passwords further increase the risk of successful brute force attacks.

---

## Remediation Recommendations

1. **Implement Account Lockout**  
   Temporarily lock an account (or IP address) after a defined number of failed attempts (e.g., 5 failed attempts → 15-minute lockout).

2. **Apply Rate Limiting**  
   Restrict the number of login requests allowed per IP address and per username within a specific time window.

3. **Add CAPTCHA**  
   Trigger a CAPTCHA challenge after a small number of failed login attempts.

4. **Deploy Web Application Firewall (WAF) Rules**  
   Detect and block rapid, repeated POST requests to the login endpoint.

5. **Enable Multi-Factor Authentication (MFA)**  
   Require an additional verification factor beyond username and password.

---

## Skills Demonstrated

- Use of Burp Suite Intruder for password brute forcing
- Analysis of server responses based on length differences
- Identification of broken authentication weaknesses
- Clear documentation of impact and practical remediation measures

---

## References

- OWASP Testing Guide – Brute Force Testing
- OWASP Authentication Cheat Sheet
- CWE-307: Improper Restriction of Excessive Authentication Attempts
