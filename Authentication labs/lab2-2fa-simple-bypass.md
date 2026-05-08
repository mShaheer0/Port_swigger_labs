# Lab 2 — 2FA Simple Bypass

## Vulnerability

The application implements two-factor authentication (2FA) but fails to enforce it properly. After completing the first authentication step (username/password), the second-factor verification page can be skipped by directly navigating to an authenticated URL.

---

## Steps to Reproduce

1. Log in with your own valid credentials and complete the 2FA step. Note the URL you land on after full authentication (e.g., `/my-account`).
2. Log out.
3. Log in as the victim user using their known credentials (username and password).
4. When redirected to the 2FA verification page, **do not** submit a code — instead, manually navigate to the post-login URL (e.g., `/my-account?id=victim-username`).
5. Observe that access is granted without completing 2FA.

---

## Key Insight

The server grants a session after the first authentication step before 2FA is verified. The 2FA page is purely a client-side redirect that can be bypassed by navigating directly to an authenticated endpoint.

---

## Impact

2FA provides no real security benefit if it can be bypassed. An attacker who obtains a victim's password gains full account access, completely negating the second factor.

---

## Remediation

- The server must **not** issue a fully authenticated session until 2FA is successfully completed.
- Use a **partial/intermediate session state** (e.g., a separate session flag) that restricts access to all resources except the 2FA page until the code is verified.
- Implement **server-side enforcement** — never rely on client-side redirects to gate authenticated pages.

---

## Tools Used

- Burp Suite (Proxy)
- Browser (manual navigation)
