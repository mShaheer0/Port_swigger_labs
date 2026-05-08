# Lab 3 — Password Reset Broken Logic

## Vulnerability

The password reset mechanism does not properly validate that the reset token belongs to the user making the request. By tampering with the request, an attacker can reset another user's password.

---

## Steps to Reproduce

1. Trigger a password reset for your own account and capture the reset request in Burp Suite.
2. Follow the reset link from your email. Intercept the `POST` request that submits the new password.
3. Observe that the request contains a `temp-forgot-password-token` parameter along with the `username` field.
4. Change the `username` parameter value to the target victim's username (e.g., `carlos`) while keeping your own (or a blank) token.
5. Forward the request. The password is reset for the victim account.
6. Log in as the victim using the newly set password.

---

## Key Insight

The server updates the password based on the `username` parameter in the POST body rather than strictly binding the token to the account it was issued for. The token is not properly validated against the username.

---

## Impact

Any authenticated or unauthenticated attacker who can issue a reset request for their own account can take over any other account by changing a single parameter.

---

## Remediation

- **Bind the reset token to a specific user account** on the server side. The token must be validated against the account it was issued for — the `username` field in the request body must not be trusted.
- Ensure tokens are **single-use** and expire quickly (e.g., 15 minutes).
- Invalidate all active sessions for an account when a password reset is completed.

---

## Tools Used

- Burp Suite (Proxy, Repeater)
- Email client (to receive reset link)
