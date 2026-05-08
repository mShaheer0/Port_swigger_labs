# Lab 6 — Broken Brute-Force Protection (IP Block)

## Vulnerability

The application blocks an IP address after a set number of consecutive failed login attempts. However, the counter resets whenever a **successful login** occurs. By alternating between a victim username and your own valid credentials, the counter can be reset repeatedly, allowing unlimited brute-force attempts against the victim account.

---

## Steps to Reproduce

1. Navigate to the login page and confirm that the account gets IP-blocked after (e.g.) 3 failed attempts.
2. Create a combined payload list in Burp Intruder where every third request is a successful login with your own credentials (to reset the counter), and the other requests attempt the victim's username with passwords from a wordlist.
3. Use a **Pitchfork** attack with two payload positions:
   - Position 1: `username` — alternates between `your-username` and `victim-username` (constructed list).
   - Position 2: `password` — alternates between `your-password` and entries from the password wordlist (constructed list).
4. Run the attack. Your successful logins reset the IP block counter after every 2 victim attempts.
5. Identify the successful response for the victim (HTTP 302 redirect or different response length/status).
6. Log in as the victim with the discovered password.

---

## Key Insight

The brute-force protection only counts *consecutive* failures. Inserting a known-good login resets the failure counter, making the protection trivially bypassable when the attacker controls one valid account on the same system.

---

## Impact


The IP-based lockout policy provides false assurance. An attacker with any valid account (even a throwaway) can perform an unbounded brute-force attack against any other account.

---

## Remediation

- Track failed login attempts **per username** (not just per IP), so resetting the counter via a different account has no effect.
- Implement **absolute lockout** (e.g., lock an account after N total failed attempts regardless of intermixed successes).
- Use **CAPTCHA** or MFA to add friction that automation cannot easily bypass.
- Do not rely solely on IP-based controls — use a combination of IP, username, and device fingerprint signals.

---

## Tools Used

- Burp Suite (Proxy, Intruder — Pitchfork mode)
- PortSwigger Password Wordlist
- Custom payload lists (alternating credentials)
