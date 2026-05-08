# Lab 1 — Username Enumeration via Different Responses

## Vulnerability

The login endpoint returns different error messages depending on whether the submitted username exists in the system. An attacker can exploit this behavior to enumerate valid usernames before attempting password brute-force.

---

## Steps to Reproduce

1. Navigate to the login page.
2. Submit a request with an invalid username and any password. Note the error message (e.g., *"Invalid username"*).
3. Submit a request with a valid username and a wrong password. Note the different error message (e.g., *"Incorrect password"*).
4. Use Burp Suite Intruder with a username wordlist to identify which usernames return the *"Incorrect password"* response — those are valid accounts.
5. With confirmed valid usernames, run a second Intruder attack using a password wordlist to brute-force the password.
6. Log in with the discovered credentials to complete the lab.

---

## Key Insight

The server inadvertently leaks whether a username exists by returning semantically different error messages. Even subtle differences in wording are enough for enumeration.

---

## Impact

An attacker can quickly narrow down valid accounts from a wordlist, dramatically reducing the effort required for a credential-stuffing or brute-force attack.

---

## Remediation

- Return a **generic error message** for all failed login attempts (e.g., *"Invalid username or password"*), regardless of which field is incorrect.
- Implement **account lockout** or **rate limiting** after a number of failed attempts.
- Consider **multi-factor authentication (MFA)** to reduce the value of a compromised password alone.

---

## Tools Used

- Burp Suite (Proxy, Intruder)
- PortSwigger Username &  Password Wordlists
