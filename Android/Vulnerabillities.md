# 📱 Mobile Application Vulnerabilities

A practical checklist of common **Android mobile application vulnerabilities and security weaknesses** for authorized penetration testing, security assessments, CTFs, and lab environments.

> ⚠️ **Disclaimer:** Perform security testing only on applications, devices, APIs, and environments where you have explicit authorization.

---

## 📑 Table of Contents

* [1. Authentication](#1-authentication)
* [2. Authorization](#2-authorization)
* [3. Session Management](#3-session-management)
* [4. JWT and Token Security](#4-jwt-and-token-security)
* [5. Insecure Data Storage](#5-insecure-data-storage)
* [6. Cryptography](#6-cryptography)
* [7. Network Security](#7-network-security)
* [8. Android Components](#8-android-components)
* [9. Deep Links and URI Handling](#9-deep-links-and-uri-handling)
* [10. WebView Security](#10-webview-security)
* [11. Intent Security](#11-intent-security)
* [12. Content Providers](#12-content-providers)
* [13. Broadcast Receivers](#13-broadcast-receivers)
* [14. Services](#14-services)
* [15. Backup and Data Exposure](#15-backup-and-data-exposure)
* [16. Sensitive Information Disclosure](#16-sensitive-information-disclosure)
* [17. File Handling](#17-file-handling)
* [18. Platform Security](#18-platform-security)
* [19. Runtime Protection](#19-runtime-protection)
* [20. Biometric Security](#20-biometric-security)
* [21. OTP and Verification](#21-otp-and-verification)
* [22. Account Recovery](#22-account-recovery)
* [23. Account Deletion](#23-account-deletion)
* [24. Client-Side Security](#24-client-side-security)
* [25. API Security](#25-api-security)
* [26. Privacy Issues](#26-privacy-issues)
* [27. Testing Techniques vs Findings](#27-testing-techniques-vs-findings)
* [28. Evidence Collection](#28-evidence-collection)
* [29. Final Checklist](#29-final-checklist)

---

# 1. Authentication

Authentication vulnerabilities occur when an application does not properly verify the identity of the user.

### Common Issues

* Weak authentication mechanisms
* Authentication bypass
* Missing authentication
* Password policy weaknesses
* Username/email enumeration
* Brute-force protection issues
* Missing rate limiting
* OTP authentication bypass
* OTP reuse
* OTP prediction
* OTP rate-limit bypass
* Password reset flaws
* Email verification bypass
* Account registration bypass
* Login logic flaws
* Authentication state confusion

### Things to Test

```text
Login
 ├── Invalid credentials
 ├── Empty credentials
 ├── Account enumeration
 ├── Rate limiting
 ├── Brute-force protection
 ├── Account lockout
 ├── OTP
 ├── MFA
 └── Session creation
```

### Remediation

* Enforce authentication **server-side** for every protected endpoint.
* Use strong password requirements appropriate to the application's risk.
* Implement rate limiting and abuse detection for login and verification endpoints.
* Use MFA for sensitive accounts and high-risk operations.
* Avoid revealing whether a username/email exists.
* Implement secure account lockout or progressive throttling where appropriate.
* Generate authentication challenges and verification codes using a cryptographically secure random generator.
* Expire authentication challenges after a short, defined period.
* Invalidate authentication state after critical security events such as password changes.
* Do not rely on hidden UI elements or local application state to enforce authentication.

---

# 2. Authorization

Authorization determines whether an authenticated user is allowed to access a resource or perform an action.

### Common Issues

* IDOR
* BOLA
* Horizontal privilege escalation
* Vertical privilege escalation
* Missing authorization
* Client-side authorization
* Function-level authorization bypass
* Object-level authorization bypass
* Administrative functionality exposure

### Example

User A:

```http
GET /api/user/1001/profile
```

Changing:

```text
1001 → 1002
```

and receiving User B's information may indicate an **IDOR/BOLA vulnerability** if server-side authorization is missing.

> Changing an ID and receiving `200 OK` alone is not enough. Verify whether unauthorized data or functionality is actually accessible.

### Remediation

* Perform authorization checks **server-side** on every protected request.
* Verify that the authenticated user is authorized to access the requested object.
* Do not trust user IDs, roles, permissions, or ownership information supplied by the mobile client.
* Implement centralized authorization middleware/policies.
* Apply least privilege to administrative functions.
* Deny access by default.
* Use object-level and function-level authorization checks.
* Test authorization with multiple user roles and accounts.
* Return appropriate errors without exposing sensitive resource information.

---

# 3. Session Management

Test how sessions are created, maintained, invalidated, and revoked.

### Common Issues

* Session not invalidated after logout
* Long-lived sessions
* Session fixation
* Session reuse
* Multiple active sessions without controls
* Token reuse after password change
* Token reuse after account deletion
* Missing session expiration
* Insecure refresh token handling
* Weak session revocation

### Test Cases

```text
Login
   ↓
Capture session/token
   ↓
Logout
   ↓
Replay token
   ↓
Check whether authenticated functionality remains accessible
```

### Remediation

* Use short-lived access tokens where practical.
* Implement secure refresh-token handling.
* Revoke or rotate tokens after appropriate security events.
* Invalidate sessions after logout where the application's threat model requires it.
* Invalidate or reauthenticate sessions after password changes or account compromise.
* Bind sensitive operations to the authenticated server-side session.
* Avoid storing long-lived credentials unnecessarily.
* Maintain server-side session state when immediate revocation is required.

---

# 4. JWT and Token Security

Mobile applications frequently use JWTs, OAuth tokens, access tokens, and refresh tokens.

### Test

* Token expiration
* `iat` validation
* `exp` validation
* Token reuse
* Refresh token rotation
* Token revocation
* Token storage
* Token leakage
* Token exposure in URLs
* Token exposure in backups
* Algorithm configuration
* JWT claim validation
* Authorization based on client-controlled claims

### Example JWT Claims

```json
{
  "iss": "api.example.com",
  "sub": "user",
  "iat": 1791194030,
  "exp": 1791197630
}
```

### Remediation

* Validate JWT signatures on the server.
* Explicitly allow only approved signing algorithms.
* Validate issuer, audience, expiration, and other security-relevant claims.
* Use short-lived access tokens.
* Rotate refresh tokens where appropriate.
* Implement refresh-token reuse detection.
* Never trust JWT claims solely because they were supplied by the client.
* Protect signing keys and keep them out of the mobile application.
* Do not place tokens in URLs.
* Store tokens using appropriate platform-protected storage.

---

# 5. Insecure Data Storage

Sensitive information should not be stored insecurely on the device.

### Possible Sensitive Data

* Passwords
* Authentication tokens
* Refresh tokens
* API keys
* Personal information
* Payment information
* Session identifiers
* JWTs
* Encryption keys
* Database credentials

### Places to Inspect

```text
SharedPreferences
SQLite databases
Internal storage
External storage
Cache
Logs
Clipboard
Application files
Backup data
WebView storage
Cookies
```

### Remediation

* Avoid storing sensitive information unless necessary.
* Never store plaintext passwords.
* Use Android Keystore-backed cryptographic keys where appropriate.
* Use encrypted storage mechanisms for sensitive local data.
* Minimize token lifetime and local persistence.
* Restrict sensitive files to application-private storage.
* Avoid storing secrets in external/shared storage.
* Remove sensitive data from caches when no longer required.
* Ensure backup policies do not unintentionally expose sensitive data.
* Clear sensitive data when accounts are removed or data is no longer needed.

---

# 6. Cryptography

Weak or incorrectly implemented cryptography can expose sensitive information.

### Common Issues

* Weak encryption algorithms
* Deprecated algorithms
* ECB mode
* Hardcoded encryption keys
* Hardcoded IVs
* Static IVs
* Weak key generation
* Weak random number generation
* Improper hashing
* Unsalted password hashing
* Hardcoded cryptographic secrets
* Incorrect cryptographic implementation

### Look For

```java
Cipher.getInstance(...)
SecretKeySpec(...)
IvParameterSpec(...)
MessageDigest.getInstance(...)
KeyGenerator(...)
SecureRandom(...)
```

### Remediation

* Prefer well-reviewed platform cryptographic APIs.
* Use modern, authenticated encryption where appropriate.
* Generate keys using cryptographically secure mechanisms.
* Do not hardcode encryption keys in the APK.
* Do not reuse IVs/nonces where the selected algorithm requires uniqueness.
* Use cryptographically secure randomness for security-sensitive values.
* Use password-hashing algorithms designed for passwords rather than general-purpose hashes.
* Keep cryptographic key material in protected platform facilities where practical.
* Define and document key rotation and lifecycle procedures.
* Avoid implementing custom cryptographic algorithms.

---

# 7. Network Security

Mobile applications communicate with backend servers through APIs.

### Test

* HTTP communication
* HTTPS configuration
* TLS versions
* Certificate validation
* Hostname validation
* Certificate pinning
* Sensitive data transmission
* Authentication headers
* Authorization headers
* Token exposure
* API endpoint exposure
* Insecure redirects

### Cleartext Traffic

Check whether sensitive information is transmitted through:

```text
HTTP
```

instead of:

```text
HTTPS
```

### Remediation

* Use HTTPS for sensitive communication.
* Configure TLS using current secure defaults.
* Validate both the server certificate chain and hostname.
* Disable cleartext traffic unless there is a documented, controlled requirement.
* Do not implement custom certificate-validation logic that weakens platform validation.
* Keep TLS libraries and networking dependencies updated.
* Avoid transmitting credentials or tokens through URLs.
* Use secure redirect handling.
* Consider certificate pinning where the application's threat model justifies it, while planning a safe certificate-rotation strategy.

---

# 8. Android Components

Android applications consist of multiple components.

```text
Activity
Service
Broadcast Receiver
Content Provider
```

Inspect:

```text
AndroidManifest.xml
```

### Common Issues

* Exported activities
* Exported services
* Exported receivers
* Exported providers
* Missing permissions
* Weak component protection
* Sensitive functionality exposed externally

### Important

An exported component **by itself is not automatically a vulnerability**.

The security impact depends on what the component allows an untrusted application or external caller to do.

### Remediation

* Set `android:exported="false"` for components that do not need external access.
* Explicitly protect externally accessible components with appropriate permissions.
* Minimize the application's exported attack surface.
* Validate all externally supplied input.
* Do not assume callers are trusted because they are other Android applications.
* Protect sensitive operations with authorization checks.
* Review exported components after every manifest change.
* Include component testing in release security testing.

---

# 9. Deep Links and URI Handling

Applications may expose custom schemes or Android App Links.

### Examples

```text
myapp://
https://example.com/app/
```

### Test

* Authentication bypass
* Sensitive activity exposure
* Parameter manipulation
* Open redirects
* Token leakage
* WebView loading
* Host validation
* Path validation
* Parameter injection
* Unauthorized functionality

### Example

```text
myapp://reset-password?token=XXXX
```

### Remediation

* Prefer verified Android App Links where appropriate.
* Explicitly validate allowed hosts, paths, and parameters.
* Do not trust URI parameters as authorization decisions.
* Require authentication for sensitive functionality.
* Avoid putting long-lived secrets in URLs.
* Reject unexpected URI schemes and hosts.
* Canonicalize and validate URLs before processing them.
* Do not automatically load untrusted URLs into privileged application contexts.

---

# 10. WebView Security

WebViews allow applications to display web content.

### Common Issues

* JavaScript enabled unnecessarily
* JavaScript interface exposure
* Untrusted URL loading
* SSL validation weaknesses
* File access enabled
* Universal access from file URLs
* URL validation weaknesses
* Local file access
* Sensitive information exposure

### Look For

```java
WebView
WebViewClient
WebSettings
setJavaScriptEnabled(true)
addJavascriptInterface(...)
loadUrl(...)
```

### Remediation

* Disable JavaScript unless required.
* Restrict WebView navigation to trusted origins.
* Validate URLs before loading them.
* Avoid exposing sensitive native methods through JavaScript interfaces.
* Use HTTPS for WebView content.
* Avoid unnecessary file and content access.
* Keep WebView configuration restrictive by default.
* Do not override TLS errors by blindly accepting invalid certificates.
* Keep Android System WebView and application dependencies updated.

---

# 11. Intent Security

Android Intents are used for communication between application components.

### Common Issues

* Intent injection
* Intent redirection
* Sensitive data in intents
* Untrusted intent extras
* Implicit intent abuse
* Missing permission checks

### Inspect

```java
getIntent()
getStringExtra()
getParcelableExtra()
startActivity()
startService()
sendBroadcast()
```

### Remediation

* Validate all intent extras received from external callers.
* Prefer explicit intents for sensitive internal communication.
* Restrict exported components.
* Use permissions to protect sensitive IPC operations.
* Do not trust intent data for authentication or authorization.
* Validate destination components before redirecting intents.
* Avoid placing unnecessary sensitive information in intent extras.

---

# 12. Content Providers

Content Providers can expose application data to other applications.

### Test

* Exported providers
* Read access
* Write access
* Missing permissions
* URI manipulation
* Path traversal
* Sensitive database exposure
* SQL injection in provider queries

### Example

```text
content://com.example.app/users
```

### Remediation

* Set providers to non-exported unless external access is required.
* Apply appropriate read/write permissions.
* Validate content-provider URIs and path parameters.
* Use parameterized database queries.
* Restrict access to the minimum required data.
* Avoid exposing sensitive database tables unnecessarily.
* Validate caller permissions before performing sensitive operations.

---

# 13. Broadcast Receivers

Broadcast Receivers process broadcast messages.

### Test

* Exported receivers
* Permission protection
* Sensitive broadcasts
* Spoofed broadcasts
* Unauthorized triggering
* Sensitive data in broadcast extras

### Example

```xml
<receiver
    android:name=".ExampleReceiver"
    android:exported="true" />
```

### Remediation

* Set receivers to non-exported when external communication is unnecessary.
* Require appropriate permissions for protected broadcasts.
* Validate all broadcast extras.
* Do not trust broadcast senders without appropriate protection.
* Avoid transmitting sensitive information through unrestricted broadcasts.
* Review dynamically registered receivers as well as manifest-declared receivers.

---

# 14. Services

Services can perform background operations.

### Test

* Exported services
* Missing permissions
* Unauthorized service invocation
* Sensitive parameters
* Intent manipulation
* Authentication bypass

### Remediation

* Set services to non-exported unless external invocation is required.
* Protect externally callable services with appropriate permissions.
* Validate all incoming parameters.
* Enforce authorization before sensitive operations.
* Avoid exposing privileged background functionality to arbitrary applications.
* Review both started and bound services.

---

# 15. Backup and Data Exposure

Android backup mechanisms can expose sensitive application data.

### Test

* Backup enabled
* Sensitive files included in backups
* SharedPreferences backup
* Database backup
* Authentication token backup
* Cloud backup exposure
* Device transfer exposure

### Remediation

* Define an explicit backup strategy.
* Exclude sensitive files from backups when appropriate.
* Do not allow credentials or long-lived secrets to be unnecessarily backed up.
* Review Android backup configuration during release testing.
* Consider the application's threat model when deciding which data may be restored.
* Verify backup/restore behavior on supported Android versions.

---

# 16. Sensitive Information Disclosure

Sensitive information may accidentally appear in application resources or runtime output.

### Search For

```text
API keys
Access tokens
Passwords
Secrets
Internal URLs
Debug endpoints
Cloud credentials
Database credentials
Private keys
User information
Internal hostnames
```

### Useful Static Searches

```text
api
token
secret
password
apikey
authorization
bearer
firebase
aws
client_secret
private_key
```

### Remediation

* Remove secrets from source code and APK resources.
* Never embed server-side credentials in a mobile application.
* Move sensitive operations to trusted backend infrastructure.
* Use environment-specific configuration safely.
* Rotate credentials that have been exposed.
* Remove sensitive information from production logs.
* Minimize diagnostic information in production builds.
* Scan source repositories and build artifacts for secrets.

> **Important:** Obfuscating a secret does not make it a secure secret. Anything embedded in an APK should be considered potentially recoverable.

---

# 17. File Handling

Applications frequently create or process files.

### Test

* Path traversal
* Unsafe file extraction
* Malicious file handling
* Insecure temporary files
* Sensitive file exposure
* External storage exposure
* File overwrite
* Unsafe MIME validation
* File extension validation
* FileProvider configuration

### Remediation

* Validate and canonicalize file paths.
* Prevent user-controlled paths from escaping intended directories.
* Avoid trusting file extensions as security controls.
* Validate file types and content where necessary.
* Store sensitive files in application-private storage.
* Use secure temporary-file handling.
* Configure `FileProvider` with the minimum required paths.
* Avoid exposing arbitrary filesystem locations through content URIs.

---

# 18. Platform Security

Check whether the application uses Android security controls correctly.

### Test

* `android:debuggable`
* `android:allowBackup`
* Network Security Configuration
* Exported components
* Permissions
* FileProvider
* PendingIntent configuration
* Task configuration
* Clipboard handling
* Screenshot protection

### Example

```xml
android:debuggable="true"
```

A debuggable production build can increase the attack surface, but severity depends on the deployment context and actual impact.

### Remediation

* Disable debugging in production builds.
* Use release build configurations for production.
* Review `android:exported` settings.
* Minimize requested permissions.
* Configure backup behavior intentionally.
* Protect sensitive activities from inappropriate screenshot exposure where required.
* Use immutable/mutability-restricted `PendingIntent` configurations as appropriate.
* Keep target SDK and Android dependencies current.
* Perform release-build security testing rather than testing only debug builds.

---

# 19. Runtime Protection

Mobile applications may implement runtime defenses.

### Areas

* Root detection
* Emulator detection
* Debugger detection
* Frida detection
* Hook detection
* Tamper detection
* Runtime integrity checks
* Code obfuscation
* RASP
* Play Integrity

### Important

Bypassing a security control is generally a **testing technique**, not automatically a vulnerability.

For example:

```text
Frida bypasses root detection
```

does not automatically mean the application has a vulnerability.

The finding depends on what security-sensitive functionality becomes accessible as a result.

### Remediation

Where runtime protections are justified by the threat model:

* Perform security-sensitive authorization server-side.
* Use runtime integrity mechanisms as defense-in-depth rather than the sole security boundary.
* Protect sensitive operations with server-side risk controls.
* Use appropriate application integrity mechanisms.
* Apply code obfuscation where appropriate.
* Monitor suspicious runtime behavior on supported platforms.
* Avoid relying solely on root or emulator detection to protect critical data.
* Design graceful failure behavior when integrity checks fail.

---

# 20. Biometric Security

Test biometric authentication implementations.

### Common Issues

* Biometric check performed only on the client
* Sensitive operation performed without server-side authorization
* Authentication bypass
* Weak fallback mechanism
* Improper session binding
* Biometric state not properly validated

### Test Sensitive Operations

```text
Payment
Password change
Account recovery
Profile changes
Transaction authorization
```

### Remediation

* Use Android's supported biometric authentication APIs.
* Protect sensitive cryptographic keys with appropriate Android Keystore controls.
* Bind key usage to the required authentication state where appropriate.
* Do not treat a client-side boolean such as `biometricSuccess=true` as proof of authorization.
* Require server-side authorization for security-sensitive backend operations.
* Design secure fallback authentication mechanisms.
* Re-authenticate for particularly sensitive operations when appropriate.

---

# 21. OTP and Verification

### Test

* OTP reuse
* OTP prediction
* OTP brute force
* Missing rate limiting
* OTP expiration
* OTP replay
* OTP leakage
* OTP bypass
* Client-side OTP validation
* Verification-state manipulation

### Example Workflow

```text
Request OTP
   ↓
Receive OTP
   ↓
Modify request
   ↓
Test validation
   ↓
Test expiration
   ↓
Test reuse
   ↓
Test rate limiting
```

### Remediation

* Generate OTPs using cryptographically secure randomness.
* Make OTPs short-lived.
* Enforce server-side OTP validation.
* Invalidate OTPs after successful use.
* Apply rate limits and attempt limits.
* Bind verification challenges to the appropriate account/session/purpose.
* Do not expose OTP values through logs or client-side debug information.
* Do not treat a local verification flag as proof that the server-side verification succeeded.

---

# 22. Account Recovery

Test password reset and account recovery functionality.

### Common Issues

* Token prediction
* Token reuse
* Expired token acceptance
* Missing token invalidation
* Account enumeration
* Email manipulation
* Phone-number manipulation
* OTP bypass
* Host-header-related reset issues
* Client-side validation

### Remediation

* Generate high-entropy, single-use recovery tokens.
* Set short expiration periods.
* Invalidate tokens after successful use.
* Bind recovery challenges to the intended account.
* Do not allow user-controlled parameters to select the recovery target without proper authorization.
* Apply rate limiting.
* Avoid account enumeration.
* Revoke appropriate existing sessions after credential recovery.
* Keep recovery logic server-side.

---

# 23. Account Deletion

Account deletion workflows should properly invalidate sessions and remove or restrict access to deleted accounts.

### Test

```text
Login
 ↓
Capture session
 ↓
Delete account
 ↓
Replay previous session
 ↓
Access authenticated endpoints
```

Check whether the backend properly handles:

* Existing sessions
* Refresh tokens
* Access tokens
* API authorization
* Cached data
* Sensitive application data

### Remediation

* Mark deleted accounts as inaccessible immediately.
* Revoke active sessions and refresh tokens.
* Ensure API authorization checks account state.
* Remove or appropriately retain personal data according to documented requirements.
* Clear locally cached sensitive account information.
* Prevent deleted users from accessing authenticated APIs with previously issued credentials.
* Test deletion and re-registration edge cases.

---

# 24. Client-Side Security

Never assume that a mobile client can safely enforce authorization.

### Test

* Client-side role checks
* Hidden UI functionality
* Disabled buttons
* Local flags
* Boolean security controls
* Local authentication state
* Client-controlled user IDs
* Client-controlled roles
* Client-controlled prices
* Client-controlled transaction values

### Example

```text
isAdmin = true
```

Changing a local value is only relevant if the backend trusts that value for authorization.

### Remediation

* Treat the mobile application as an untrusted client.
* Perform authorization on the backend.
* Recalculate security-sensitive values server-side.
* Do not trust client-provided roles or permissions.
* Do not use hidden UI elements as security controls.
* Validate all security-sensitive parameters on the server.
* Use server-side business logic for pricing, transactions, permissions, and account state.

---

# 25. API Security

Mobile applications heavily depend on APIs.

### Test

* IDOR/BOLA
* Broken authorization
* Mass assignment
* Excessive data exposure
* Rate limiting
* Authentication bypass
* Parameter tampering
* HTTP method manipulation
* CORS
* Injection
* Improper error handling
* Business logic flaws
* Race conditions
* Missing security headers
* Sensitive information disclosure

### Common Workflow

```text
Mobile Application
        ↓
Burp Suite
        ↓
Capture API Request
        ↓
Understand Parameters
        ↓
Modify Request
        ↓
Replay
        ↓
Verify Authorization
        ↓
Validate Impact
```

### Remediation

* Authenticate every protected API request.
* Implement object-level authorization.
* Implement function-level authorization.
* Validate all input server-side.
* Use allowlists for accepted fields where appropriate.
* Prevent mass assignment through explicit request models.
* Apply rate limiting and abuse controls.
* Return only the data required by the client.
* Implement proper transaction and business-logic validation.
* Use parameterized database queries.
* Handle concurrent operations safely.
* Keep sensitive API operations behind server-side controls.
* Do not assume requests originated from the official mobile application.

---

# 26. Privacy Issues

Mobile applications process significant amounts of personal information.

### Test

* Excessive permissions
* Location leakage
* Contact leakage
* Device information leakage
* Advertising identifiers
* Analytics data
* Sensitive information in logs
* Sensitive information in crash reports
* Clipboard access
* Screenshots
* Notification leakage
* Third-party SDK data collection

### Remediation

* Request only permissions required for application functionality.
* Request permissions at the appropriate time and explain their purpose.
* Minimize collection of personal information.
* Avoid transmitting unnecessary device identifiers.
* Protect personal data both in transit and at rest.
* Remove sensitive data from logs and crash reports.
* Review third-party SDK data collection.
* Prevent sensitive content from appearing in notifications where appropriate.
* Avoid unnecessary clipboard access.
* Define appropriate retention and deletion policies.
* Document privacy-sensitive data flows.

---

# 27. Testing Techniques vs Findings

This distinction is extremely important during mobile penetration testing.

| Observation / Technique                           | Automatically a Vulnerability? |
| ------------------------------------------------- | ------------------------------ |
| APK can be decompiled                             | ❌ No                           |
| JADX reveals source code                          | ❌ No                           |
| APK can be patched                                | ❌ No                           |
| Frida can hook a method                           | ❌ No                           |
| SSL pinning can be bypassed                       | ❌ No                           |
| Root detection can be bypassed                    | ❌ No                           |
| Activity is exported                              | ❌ Not necessarily              |
| Debuggable APK                                    | ⚠️ Context dependent           |
| Sensitive token stored insecurely                 | ✅ Potential finding            |
| Unauthorized API access                           | ✅ Finding                      |
| Sensitive data exposed through exported component | ✅ Finding                      |
| Invalid TLS certificate accepted                  | ✅ Finding                      |
| Authentication bypass                             | ✅ Finding                      |
| Authorization bypass                              | ✅ Finding                      |
| Session remains valid after required revocation   | ✅ Potential finding            |

### Golden Rule

> **A testing technique is not necessarily a vulnerability. Validate the actual security impact.**

### Remediation Principle

Remediation should address the **underlying security boundary**, not merely make the testing technique harder.

For example:

```text
Bad approach:
"Prevent Frida from attaching."

Better approach:
"Ensure sensitive authorization decisions are enforced server-side."
```

Similarly:

```text
Bad approach:
"Hide the admin button."

Better approach:
"Require server-side authorization for administrative functionality."
```

---

# 28. Evidence Collection

For every confirmed finding, collect sufficient evidence.

### Recommended Evidence

```text
1. Vulnerable request
2. Vulnerable response
3. Screenshots
4. Relevant APK/code location
5. Manifest configuration
6. API endpoint
7. Request modification
8. Response comparison
9. Impact demonstration
10. Reproduction steps
```

### Report Structure

```text
Title
Severity
CVSS
OWASP / MASVS Mapping

Description

Impact

Steps to Reproduce

Proof of Concept

Affected Component

Evidence

Remediation

References
```

### Remediation Evidence

Where possible, verify the fix rather than only confirming that code changed.

```text
Original behavior
       ↓
Vulnerability confirmed
       ↓
Developer remediation
       ↓
Retest original PoC
       ↓
Verify vulnerability no longer exists
       ↓
Test for regression
```

---

# 29. Final Checklist

## 🔐 Authentication

* [ ] Login
* [ ] Registration
* [ ] Password policy
* [ ] MFA
* [ ] OTP
* [ ] Email verification
* [ ] Password reset
* [ ] Account recovery
* [ ] Account lockout
* [ ] Rate limiting
* [ ] Server-side authentication
* [ ] Authentication-state validation

## 👤 Authorization

* [ ] IDOR/BOLA
* [ ] Horizontal privilege escalation
* [ ] Vertical privilege escalation
* [ ] Function-level authorization
* [ ] Object-level authorization
* [ ] Client-side authorization
* [ ] Administrative functionality
* [ ] Server-side access control

## 🔑 Session & Tokens

* [ ] Session expiration
* [ ] Logout invalidation
* [ ] Token expiration
* [ ] Refresh tokens
* [ ] JWT validation
* [ ] Token reuse
* [ ] Session fixation
* [ ] Account deletion session invalidation
* [ ] Password-change session invalidation

## 💾 Storage

* [ ] SharedPreferences
* [ ] SQLite
* [ ] Internal storage
* [ ] External storage
* [ ] Cache
* [ ] Logs
* [ ] Clipboard
* [ ] Cookies
* [ ] WebView storage
* [ ] Backup
* [ ] Android Keystore usage

## 🔒 Cryptography

* [ ] Weak algorithms
* [ ] Hardcoded keys
* [ ] Hardcoded IV
* [ ] Weak randomness
* [ ] Weak hashing
* [ ] Improper key management
* [ ] Key rotation

## 🌐 Network

* [ ] HTTPS
* [ ] TLS validation
* [ ] Certificate validation
* [ ] Hostname validation
* [ ] Certificate pinning
* [ ] Cleartext traffic
* [ ] Sensitive data transmission
* [ ] Secure redirects

## 📱 Android Components

* [ ] Activities
* [ ] Services
* [ ] Broadcast Receivers
* [ ] Content Providers
* [ ] Exported components
* [ ] Permissions
* [ ] Intent handling
* [ ] PendingIntents
* [ ] IPC authorization

## 🔗 Deep Links

* [ ] Custom schemes
* [ ] App Links
* [ ] URI validation
* [ ] Parameter manipulation
* [ ] Authentication bypass
* [ ] Open redirect
* [ ] Token leakage
* [ ] Host validation

## 🌍 WebView

* [ ] JavaScript
* [ ] JavaScript interfaces
* [ ] URL validation
* [ ] SSL validation
* [ ] File access
* [ ] Local file access
* [ ] Untrusted content
* [ ] Navigation restrictions

## 🛡️ Runtime

* [ ] Root detection
* [ ] Emulator detection
* [ ] Debugger detection
* [ ] Frida detection
* [ ] Hook detection
* [ ] Tamper detection
* [ ] RASP
* [ ] Integrity controls
* [ ] Server-side enforcement

## 🔌 API

* [ ] Authentication
* [ ] Authorization
* [ ] IDOR/BOLA
* [ ] Mass assignment
* [ ] Rate limiting
* [ ] Parameter tampering
* [ ] Business logic
* [ ] Sensitive data exposure
* [ ] HTTP method manipulation
* [ ] Input validation
* [ ] Race conditions

## 🔐 Privacy

* [ ] Permissions
* [ ] Location
* [ ] Contacts
* [ ] Device identifiers
* [ ] Analytics
* [ ] Logs
* [ ] Crash reports
* [ ] Clipboard
* [ ] Notifications
* [ ] Third-party SDKs
* [ ] Data retention

---

# 🧰 Useful Tools

| Tool                        | Purpose                            |
| --------------------------- | ---------------------------------- |
| **ADB**                     | Android device interaction         |
| **JADX**                    | APK/source-code analysis           |
| **APKTool**                 | APK decoding/rebuilding            |
| **MobSF**                   | Automated mobile security analysis |
| **Frida**                   | Runtime instrumentation            |
| **Objection**               | Mobile runtime testing             |
| **Burp Suite**              | HTTP/API interception              |
| **Ghidra**                  | Native binary analysis             |
| **Drozer**                  | Android IPC/component testing      |
| **apksigner**               | APK signature verification         |
| **zipalign**                | APK alignment                      |
| **Android Studio Emulator** | Android testing environment        |

---

# 📚 Recommended Testing Workflow

```text
                    APK
                     │
                     ▼
             ┌───────────────┐
             │ Recon / Scope │
             └───────┬───────┘
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
   Static Analysis       Dynamic Analysis
          │                     │
          ▼                     ▼
   JADX / MobSF /        ADB / Frida /
   APKTool / Ghidra      Objection
          │                     │
          └──────────┬──────────┘
                     ▼
              Network Analysis
                     │
                     ▼
                 Burp Suite
                     │
                     ▼
               API Testing
                     │
                     ▼
             Vulnerability Validation
                     │
                     ▼
                Evidence
                     │
                     ▼
                  Report
                     │
                     ▼
               Remediation
                     │
                     ▼
                   Retest
```

---

# 🎯 Final Takeaway

A complete mobile penetration test should not focus only on reverse engineering or APK analysis.

A strong assessment combines:

```text
Static Analysis
       +
Dynamic Analysis
       +
Network Analysis
       +
API Security Testing
       +
Android Component Testing
       +
Business Logic Testing
       +
Data Storage Testing
       +
Authentication & Authorization Testing
       +
Privacy Testing
       +
Remediation Validation
```

The objective is not simply to find unusual behavior.

The objective is to determine:

> **Can an attacker abuse the behavior to compromise confidentiality, integrity, availability, authentication, authorization, or user privacy?**

And for every confirmed issue:

> **Identify the affected security boundary, provide evidence of impact, recommend a concrete remediation, and retest the fix.**
