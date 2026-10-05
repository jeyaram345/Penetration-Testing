# Authentication Security Testing Checklist

> A practical checklist for assessing authentication and identity-management security during authorized penetration tests, bug bounty programs, CTFs, and security assessments.

---

## Table of Contents

- [1. Reconnaissance & Mapping](#1-reconnaissance--mapping)
- [2. Basic Authentication Testing](#2-basic-authentication-testing)
- [3. Intermediate Authentication Testing](#3-intermediate-authentication-testing)
- [4. Advanced Authentication Testing](#4-advanced-authentication-testing)
- [5. Modern Application Considerations](#5-modern-application-considerations)
- [6. Suggested Toolchain](#6-suggested-toolchain)
- [7. Testing Workflow](#7-testing-workflow)
- [8. Quick Authentication Checklist](#8-quick-authentication-checklist)
- [9. Disclaimer](#9-disclaimer)

---

# 1. Reconnaissance & Mapping

**Always start here.**

Before testing authentication controls, identify all authentication-related entry points and understand how identity flows through the application.

## 1.1 Identify Authentication Entry Points

- [ ] Login
- [ ] Registration / Sign-up
- [ ] Password reset
- [ ] Account recovery
- [ ] Email verification
- [ ] Phone verification
- [ ] MFA enrollment
- [ ] MFA verification
- [ ] SSO login
- [ ] OAuth / OIDC callbacks
- [ ] API authentication endpoints
- [ ] API token generation
- [ ] Refresh-token endpoints
- [ ] Mobile application authentication
- [ ] Admin / back-office authentication
- [ ] Passwordless authentication
- [ ] WebAuthn / FIDO2 authentication

## 1.2 Identify the Authentication Technology

Look for:

- Session cookies
- JWTs
- OAuth 2.0
- OpenID Connect (OIDC)
- SAML
- WebAuthn / FIDO2
- Firebase Authentication
- Auth0
- Okta
- AWS Cognito
- Custom authentication implementations

For JWTs, inspect:

- `alg`
- `typ`
- `kid`
- `iss`
- `sub`
- `aud`
- `exp`
- `iat`
- `nbf`

Also inspect JavaScript bundles and API responses for authentication-related endpoints and configuration.

## 1.3 Map Trust Boundaries

```text
Client
   |
   v
API Gateway / Reverse Proxy
   |
   v
Authentication Service
   |
   v
Application Services
   |
   v
Database / Identity Provider
```

Questions to answer:

- Which service issues authentication tokens?
- Which service validates tokens?
- Is authentication terminated at a gateway?
- Do internal services independently validate tokens?
- Can internal APIs be accessed without going through the authentication gateway?
- Are different services using different authentication mechanisms?

---

# 2. Basic Authentication Testing

## 2.1 Username Enumeration

Check whether the application behaves differently for valid and invalid accounts.

Test differences in:

- Response messages
- HTTP status codes
- Response length
- Response structure
- Response timing
- Password-reset responses
- Registration responses

Example:

```text
Invalid username:
"User does not exist"

Valid username:
"Incorrect password"
```

Ideally, both cases should produce indistinguishable responses.

---

## 2.2 Brute Force & Credential Stuffing

Assess whether authentication endpoints properly enforce:

- Rate limiting
- Account lockout
- Progressive delays
- CAPTCHA
- IP-based throttling
- Account-based throttling
- Device-based controls
- Credential-stuffing detection

Also test whether defensive controls are consistently enforced across alternate authentication endpoints.

---

## 2.3 Password Policy

Check:

- Minimum password length
- Maximum password length
- Password complexity requirements
- Common-password blocking
- Breached-password detection
- Password reuse prevention
- Password history
- Password-change requirements

Verify that password policies are enforced **server-side**.

---

## 2.4 Default & Weak Credentials

Check for:

- Vendor default credentials
- Test accounts
- Development accounts
- Demo accounts
- Forgotten administrator accounts
- Shared credentials
- Predictable usernames/passwords

---

## 2.5 Password Reset

Review:

- Reset-token randomness
- Token expiration
- Token reuse
- Token invalidation after password change
- Token leakage through URLs
- `Referer` leakage
- Host-header handling
- User/account binding
- Password-reset rate limiting
- Email/phone verification requirements

Check that a reset token cannot be reused or transferred between accounts.

---

## 2.6 Session Management

Check session cookies for:

```text
Secure
HttpOnly
SameSite
```

Test:

- Session fixation
- Session ID predictability
- Session invalidation after logout
- Session invalidation after password change
- Concurrent sessions
- Session expiration
- Idle timeout
- Absolute timeout
- Token rotation
- Session revocation

---

## 2.7 Transport Security

Verify:

- HTTPS is enforced
- HTTP redirects to HTTPS
- No credentials are transmitted over HTTP
- No mixed-content authentication requests exist
- HSTS is enabled where appropriate

---

# 3. Intermediate Authentication Testing

## 3.1 JWT Security

Review JWT implementation for:

- Algorithm validation
- `alg` handling
- Algorithm confusion
- Signature verification
- Weak HMAC secrets
- Missing signature validation
- `kid` handling
- Key-selection logic
- `exp` validation
- `nbf` validation
- `iat` validation
- Issuer validation
- Audience validation
- Token replay
- Refresh-token handling

### JWT Claims

Pay particular attention to:

```json
{
  "alg": "RS256",
  "kid": "...",
  "iss": "...",
  "sub": "...",
  "aud": "...",
  "exp": 1234567890
}
```

Check whether security-sensitive claims are actually validated by the server.

### JWT Information Disclosure

JWT payloads are generally readable by the client.

Look for:

- Passwords
- API keys
- Internal identifiers
- Sensitive personal information
- Internal service URLs
- Privileged roles
- Debug information

---

## 3.2 MFA Security

Test:

- MFA response manipulation
- Missing MFA enforcement
- MFA-step removal
- OTP brute-force protection
- OTP expiration
- OTP reuse
- OTP rate limiting
- Backup-code handling
- MFA recovery mechanisms
- "Remember this device" functionality
- Device-trust token validation
- MFA bypass through alternate login flows

Verify that MFA is enforced **server-side**, rather than relying on client-side state.

---

## 3.3 OAuth 2.0 / OIDC

Review:

- `redirect_uri` validation
- `state` parameter
- `nonce`
- Authorization-code handling
- Authorization-code reuse
- Token leakage
- Scope validation
- Client authentication
- PKCE implementation
- Token audience validation
- Issuer validation

Potential attack chain:

```text
Open Redirect
      |
      v
OAuth Callback
      |
      v
Authorization Code / Token Leakage
```

Check whether redirect URIs are strictly validated rather than relying on weak prefix or substring matching.

---

## 3.4 Password Recovery & Account Recovery

Test for:

- IDOR in recovery workflows
- User-ID manipulation
- Email/phone verification bypass
- Predictable recovery tokens
- Recovery-token reuse
- Missing expiration
- CAPTCHA bypass
- Account takeover through recovery logic
- Alternate recovery endpoints

Pay particular attention to multi-step workflows where identity information is passed between requests.

---

## 3.5 CAPTCHA Controls

Verify:

- CAPTCHA is validated server-side
- CAPTCHA responses cannot be reused
- CAPTCHA tokens expire
- CAPTCHA tokens are bound to the intended action
- Alternate endpoints cannot bypass CAPTCHA
- CAPTCHA enforcement cannot be skipped by modifying client-side parameters

---

# 4. Advanced Authentication Testing

## 4.1 SAML / SSO

For SAML implementations, review:

- XML Signature validation
- XML Signature Wrapping (XSW)
- Assertion replay protection
- `NotBefore`
- `NotOnOrAfter`
- Audience restrictions
- Issuer validation
- Destination validation
- Assertion-to-user binding
- IdP configuration
- SP configuration

Check whether assertions can be replayed or modified without invalidating authentication.

---

## 4.2 OIDC Identity Confusion

Review:

- Issuer validation
- Audience validation
- Client-ID validation
- Nonce validation
- State validation
- IdP selection
- Account linking
- Email verification

Pay special attention to applications that automatically link accounts based solely on email addresses.

---

## 4.3 Authentication Race Conditions

Test authentication-related operations for race conditions involving:

- Login attempts
- OTP verification
- Password reset
- Email verification
- Account creation
- MFA enrollment
- MFA recovery
- Session creation
- Rate-limit counters

Look for situations where concurrent requests can cause inconsistent authentication state.

---

## 4.4 Passwordless Authentication / WebAuthn

For WebAuthn/FIDO2 implementations, review:

- Origin validation
- RP ID validation
- Challenge validation
- Challenge freshness
- Credential ID handling
- Sign-count validation
- User-verification requirements
- Authentication fallback mechanisms
- Credential registration flows

Also verify that weaker authentication methods cannot be forced as a fallback.

---

## 4.5 Refresh Tokens

Check:

- Refresh-token rotation
- Refresh-token expiration
- Refresh-token revocation
- Logout behavior
- Password-change behavior
- Token reuse detection
- Token storage
- Token leakage

A stolen refresh token should not provide indefinite access when proper rotation and revocation controls are expected.

---

## 4.6 Token Storage

Identify where authentication tokens are stored:

```text
HttpOnly Cookie
Secure Cookie
localStorage
sessionStorage
IndexedDB
Application memory
Mobile secure storage
```

Assess whether the chosen storage mechanism unnecessarily exposes tokens to client-side attacks.

---

## 4.7 Federated Identity

Review account-linking behavior for:

- Unverified email addresses
- Automatic account linking
- Multiple identity providers
- Provider confusion
- Email-based account matching
- Account takeover through identity-provider switching

---

## 4.8 API Keys & Service-to-Service Authentication

Check for:

- Hardcoded API keys
- Secrets in JavaScript bundles
- Secrets in mobile applications
- Excessive API-key permissions
- Long-lived API keys
- Missing key rotation
- Missing revocation
- Weak service authentication
- Missing mTLS where required

---

## 4.9 Timing & Side-Channel Issues

Look for measurable timing differences during:

- Password verification
- Username lookup
- Token comparison
- MFA verification
- API-key validation

Authentication comparisons should use appropriate constant-time mechanisms where applicable.

---

## 4.10 Authentication Business Logic

Review:

- Concurrent sessions
- Session limits
- Step-up authentication
- Trusted-device persistence
- MFA enrollment
- MFA removal
- Password changes
- Email changes
- Recovery mechanisms
- Privileged-action authentication

Verify that sensitive operations cannot be performed using an outdated or insufficient authentication state.

---

# 5. Modern Application Considerations

## 5.1 Microservices & Zero Trust

Determine whether:

- Internal services validate tokens independently
- Services blindly trust gateway headers
- Authentication can be bypassed by reaching internal services directly
- Different services enforce different authorization rules
- Service-to-service authentication is implemented correctly

Example:

```text
                 ┌──────────────┐
Client ─────────>│ API Gateway  │
                 └──────┬───────┘
                        |
             ┌──────────┴──────────┐
             v                     v
        Service A             Service B
             |                     |
             └──────────┬──────────┘
                        v
                    Database
```

---

## 5.2 GraphQL

For GraphQL applications, test:

- Endpoint-level authentication
- Field-level authorization
- Object-level authorization
- Introspection exposure
- Resolver authorization
- Nested-object access
- Mutation authorization

An authenticated GraphQL endpoint does not necessarily mean every field and resolver is properly authorized.

---

## 5.3 Mobile Applications

Review:

- API authentication
- Certificate pinning
- Hardcoded credentials
- Embedded API keys
- Token storage
- Deep links
- Universal links
- Custom URL schemes
- Authentication callbacks
- Mobile API authorization

Pay particular attention to authentication tokens exposed through deep-link or callback mechanisms.

---

## 5.4 SPA / Headless Applications

Review:

- Token storage
- Access-token lifetime
- Refresh-token handling
- Silent token refresh
- OAuth callback handling
- CORS configuration
- CSP
- XSS impact on authentication tokens

Common client-side storage locations include:

```text
localStorage
sessionStorage
IndexedDB
Cookies
In-memory state
```

---

# 6. Suggested Toolchain

| Tool | Primary Use |
|------|-------------|
| **Burp Suite** | Proxying, request manipulation, session testing |
| **Autorize** | Authorization/access-control testing |
| **JWT Editor** | JWT inspection and testing |
| **Auth Analyzer** | Authentication workflow analysis |
| **OWASP ZAP** | Automated web security testing |
| **jwt_tool** | JWT analysis |
| **Hashcat** | Authorized password/hash auditing |
| **SAML Raider** | SAML security testing |
| **Postman** | API authentication testing |
| **Insomnia** | API testing and workflow analysis |

---

# 7. Testing Workflow

A practical authentication assessment can follow this order:

```text
1. Reconnaissance
       |
       v
2. Map authentication flows
       |
       v
3. Identify tokens and sessions
       |
       v
4. Test basic authentication controls
       |
       v
5. Test password recovery
       |
       v
6. Test MFA
       |
       v
7. Test JWT / OAuth / OIDC / SAML
       |
       v
8. Test session management
       |
       v
9. Test business logic
       |
       v
10. Test race conditions
       |
       v
11. Test API / mobile / GraphQL authentication
       |
       v
12. Document findings
```

---

# 8. Quick Authentication Checklist

## Reconnaissance

- [ ] Authentication endpoints identified
- [ ] Authentication technology identified
- [ ] Token format identified
- [ ] Session mechanism identified
- [ ] Trust boundaries mapped

## Credentials

- [ ] Username enumeration tested
- [ ] Brute-force protections tested
- [ ] Credential-stuffing protections tested
- [ ] Password policy reviewed
- [ ] Default credentials checked

## Password Recovery

- [ ] Reset tokens reviewed
- [ ] Token expiration tested
- [ ] Token reuse tested
- [ ] Account binding verified
- [ ] Recovery workflow reviewed
- [ ] CAPTCHA controls reviewed

## Sessions

- [ ] Session fixation tested
- [ ] Logout invalidation tested
- [ ] Password-change invalidation tested
- [ ] Session expiration reviewed
- [ ] Cookie flags reviewed
- [ ] Token rotation reviewed

## MFA

- [ ] MFA enforcement verified
- [ ] OTP expiration tested
- [ ] OTP reuse tested
- [ ] OTP rate limiting tested
- [ ] Backup codes reviewed
- [ ] Trusted-device mechanism reviewed

## Tokens

- [ ] JWT algorithm validation reviewed
- [ ] JWT signature validation reviewed
- [ ] JWT claims validated
- [ ] Token expiration enforced
- [ ] Refresh-token rotation reviewed
- [ ] Token storage reviewed

## OAuth / OIDC

- [ ] `redirect_uri` validation reviewed
- [ ] `state` validation reviewed
- [ ] `nonce` validation reviewed
- [ ] PKCE reviewed
- [ ] Authorization-code reuse tested
- [ ] Scope validation reviewed
- [ ] Issuer/audience validation reviewed

## SAML

- [ ] Signature validation reviewed
- [ ] Assertion validation reviewed
- [ ] Replay protection reviewed
- [ ] Time restrictions reviewed
- [ ] Audience/issuer validation reviewed

## Modern Applications

- [ ] API authentication reviewed
- [ ] GraphQL authorization reviewed
- [ ] Mobile authentication reviewed
- [ ] Deep-link authentication reviewed
- [ ] Microservice trust boundaries reviewed
- [ ] Service-to-service authentication reviewed

---

# 9. Disclaimer

This checklist is intended for **authorized security testing only**, including systems you own, have explicit permission to assess, or intentionally vulnerable environments such as CTF/lab platforms.

Always obtain appropriate authorization before testing authentication mechanisms on a real system.
