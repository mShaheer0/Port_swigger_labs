# Lab 4 — Username Enumeration via Subtly Different Responses

## Vulnerability

The login endpoint uses nearly identical error messages for invalid usernames and wrong passwords. However, a subtle difference — such as a trailing space or minor punctuation change — leaks whether a username is valid, allowing enumeration.

---

## Steps to Reproduce

1. Navigate to the login page and submit requests with various usernames and a dummy password.
2. Intercept the responses in Burp Suite and send them to Intruder.
3. Configure the username position as the payload position and load a username wordlist.
4. Run the attack and use the **Grep – Extract** feature to capture the exact error message text from each response.
5. Sort or filter results to identify any response where the error message differs subtly (e.g., missing period, extra space, different capitalisation).
6. Use the identified valid username in a second Intruder attack with a password wordlist.
7. Log in with the found credentials.

---

## Key Insight

Copy-paste errors or small inconsistencies in error message strings can reveal information even when the intent was to keep messages generic. Careful response diffing (e.g., using Burp's Grep features or comparing response lengths) exposes the difference.

---

## Impact

An attacker can enumerate valid accounts with only minor additional effort over a standard brute-force, reducing the search space for credential attacks.

---

## Remediation

- Centralise all authentication error messages through a **single code path** to guarantee identical output for all failed logins.
- Conduct a **security review of UI strings** to catch copy-paste inconsistencies before deployment.
- Apply **rate limiting** and **account lockout** to slow down enumeration attempts.

---

## Tools Used

- Burp Suite (Proxy, Intruder, Grep – Extract)
- PortSwigger Username & Password Wordlists
