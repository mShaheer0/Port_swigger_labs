# Lab 5 — Username Enumeration via Response Timing

## Vulnerability

The login endpoint processes valid and invalid usernames differently in terms of **response time**. When a valid username is submitted, the server performs additional work (e.g., password hashing), resulting in a measurably longer response time compared to requests with invalid usernames.

---


## Steps to Reproduce

1. Navigate to the login page and capture a login `POST` request in Burp Suite.
2. Notice that submitting too many requests triggers IP-based rate limiting or blocking. Bypass this by adding an `X-Forwarded-For` header with a spoofed IP and rotating the value per request.
3. Send the request to Burp Intruder and configure a **Pitchfork** attack with two payload positions:
   - Position 1: `X-Forwarded-For` value (incrementing IP list).
   - Position 2: `username` parameter (username wordlist).
4. Use a **very long password** string to amplify the timing difference for valid usernames (the server spends more time hashing a long string).
5. Run the attack and sort results by **response time** (use the Columns menu to add the response received column).
6. Requests with noticeably longer response times correspond to valid usernames.
7. Confirm the valid username with a second Intruder attack using a password wordlist.
8. Log in with the discovered credentials.

---

## Key Insight

Timing side-channels can leak information even when error messages are identical. The key amplification trick is using a very long password so the hash computation time for valid accounts is significantly greater.

---

## Impact

An attacker can enumerate valid usernames purely through response timing, without any visible difference in the HTTP response body or status code.

---

## Remediation

- **Apply consistent processing time** for all login attempts (e.g., always compute a hash regardless of whether the username exists — use a dummy hash for invalid usernames).
- Implement **IP-based rate limiting** that cannot be bypassed via headers like `X-Forwarded-For` (validate and strip untrusted forwarding headers at the perimeter).
- Use **CAPTCHA** or exponential back-off to slow automated enumeration.

---

## Tools Used

- Burp Suite (Proxy, Intruder — Pitchfork mode)
- PortSwigger Username & Password Wordlists
