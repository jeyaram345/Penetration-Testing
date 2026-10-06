# 🍎 iOS Application Vulnerabilities

A practical checklist of common **iOS application vulnerabilities and security weaknesses** for authorized penetration testing, security assessments, CTFs, and mobile security research.

> ⚠️ **Disclaimer:** Perform security testing only against applications, devices, APIs, and environments for which you have explicit authorization.

---

# 📑 Table of Contents

* [1. Authentication](#1-authentication)
* [2. Authorization](#2-authorization)
* [3. Session Management](#3-session-management)
* [4. JWT and Token Security](#4-jwt-and-token-security)
* [5. Insecure Data Storage](#5-insecure-data-storage)
* [6. Keychain Security](#6-keychain-security)
* [7. Data Protection and File Security](#7-data-protection-and-file-security)
* [8. Cryptography](#8-cryptography)
* [9. Network Security](#9-network-security)
* [10. App Transport Security](#10-app-transport-security)
* [11. SSL/TLS Certificate Validation](#11-ssltls-certificate-validation)
* [12. Certificate Pinning](#12-certificate-pinning)
* [13. URL Schemes](#13-url-schemes)
* [14. Universal Links](#14-universal-links)
* [15. WebView Security](#15-webview-security)
* [16. JavaScript Bridge Security](#16-javascript-bridge-security)
* [17. App Extensions](#17-app-extensions)
* [18. Entitlements](#18-entitlements)
* [19. Inter-Process Communication](#19-inter-process-communication)
* [20. Pasteboard Security](#20-pasteboard-security)
* [21. Screenshot and Snapshot Leakage](#21-screenshot-and-snapshot-leakage)
* [22. Logging and Sensitive Information](#22-logging-and-sensitive-information)
* [23. SQLite and Core Data](#23-sqlite-and-core-data)
* [24. Backup and Data Exposure](#24-backup-and-data-exposure)
* [25. File Protection](#25-file-protection)
* [26. Biometric Authentication](#26-biometric-authentication)
* [27. Jailbreak Detection](#27-jailbreak-detection)
* [28. Runtime Tampering](#28-runtime-tampering)
* [29. Debugging and Binary Protection](#29-debugging-and-binary-protection)
* [30. Privacy and Permission Issues](#30-privacy-and-permission-issues)
* [31. Location Data Exposure](#31-location-data-exposure)
* [32. Notification Leakage](#32-notification-leakage)
* [33. Memory and Sensitive Data Handling](#33-memory-and-sensitive-data-handling)
* [34. API Security](#34-api-security)
* [35. Business Logic](#35-business-logic)
* [36. Account Recovery](#36-account-recovery)
* [37. Account Deletion](#37-account-deletion)
* [38. Client-Side Security](#38-client-side-security)
* [39. Third-Party SDK Security](#39-third-party-sdk-security)
* [40. Testing Techniques vs Findings](#40-testing-techniques-vs-findings)
* [41. Evidence Collection](#41-evidence-collection)
* [42. Final Checklist](#42-final-checklist)

---

# 1. Authentication

Authentication vulnerabilities occur when an iOS application does not properly verify the identity of a user.

### Common Issues

* Authentication bypass
* Weak authentication
* Missing authentication
* Username/email enumeration
* Weak password policy
* Brute-force attacks
* Missing rate limiting
* MFA bypass
* OTP bypass
* OTP reuse
* Email verification bypass
* Registration bypass
* Password reset flaws
* Authentication-state manipulation

### Test

```text
Login
 ├── Valid credentials
 ├── Invalid credentials
 ├── Empty credentials
 ├── Account enumeration
 ├── Rate limiting
 ├── MFA
 ├── OTP
 └── Session creation
```

### Remediation

* Enforce authentication server-side.
* Implement rate limiting and abuse protection.
* Use MFA for sensitive operations where appropriate.
* Avoid account enumeration.
* Use secure password policies.
* Generate OTPs and authentication challenges using cryptographically secure randomness.
* Expire authentication challenges.
* Invalidate authentication state after appropriate security events.
* Never rely on local application state to prove authentication.

---

# 2. Authorization

Authorization determines whether a user is allowed to access a resource or perform an action.

### Common Issues

* IDOR
* BOLA
* Horizontal privilege escalation
* Vertical privilege escalation
* Missing authorization
* Client-side authorization
* Administrative functionality exposure
* Function-level authorization bypass

### Example

```http
GET /api/users/1001/profile
```

Changing:

```text
1001 → 1002
```

and receiving another user's information may indicate an authorization vulnerability.

> `200 OK` alone is not proof of IDOR. Confirm that unauthorized information or functionality is actually accessible.

### Remediation

* Perform authorization checks server-side.
* Validate resource ownership.
* Do not trust user IDs or roles supplied by the iOS application.
* Apply least privilege.
* Deny access by default.
* Implement centralized authorization policies.
* Test authorization with multiple accounts and roles.

---

# 3. Session Management

### Common Issues

* Session remains valid after logout
* Long-lived sessions
* Session fixation
* Session reuse
* Missing session expiration
* Refresh-token problems
* Token reuse after password change
* Token reuse after account deletion

### Test

```text
Login
 ↓
Capture session
 ↓
Logout
 ↓
Replay session
 ↓
Verify authorization
```

Also test:

```text
Password Change
        ↓
Replay old session
```

```text
Account Deletion
        ↓
Replay old session
```

### Remediation

* Use appropriately short-lived access tokens.
* Implement secure refresh-token rotation.
* Revoke sessions after security-sensitive events where appropriate.
* Invalidate sessions after logout where required by the application's security model.
* Do not unnecessarily persist long-lived credentials.
* Perform session validation server-side.

---

# 4. JWT and Token Security

iOS applications commonly use:

```text
JWT
OAuth access tokens
Refresh tokens
Session tokens
API tokens
```

### Test

* `exp`
* `iat`
* `iss`
* `aud`
* Signature validation
* Algorithm validation
* Token expiration
* Token reuse
* Refresh-token rotation
* Token revocation
* Token storage
* Token leakage

### Example

```json
{
  "iss": "api.example.com",
  "sub": "user",
  "iat": 1791194030,
  "exp": 1791197630
}
```

### Remediation

* Validate JWT signatures server-side.
* Allow only approved algorithms.
* Validate issuer and audience.
* Enforce expiration.
* Use short-lived access tokens.
* Rotate refresh tokens where appropriate.
* Protect signing keys outside the mobile application.
* Never rely solely on client-side JWT processing for authorization.

---

# 5. Insecure Data Storage

Sensitive information should not be stored insecurely on an iOS device.

### Possible Sensitive Data

```text
Passwords
Authentication tokens
Refresh tokens
API keys
PII
Payment information
Session identifiers
Encryption keys
Database credentials
```

### Places to Inspect

```text
UserDefaults
Keychain
Application Documents
Library
Caches
Temporary files
SQLite
Core Data
Cookies
WebView storage
Logs
Pasteboard
```

### Remediation

* Avoid storing unnecessary sensitive information.
* Never store plaintext passwords.
* Use Keychain for appropriate secrets.
* Apply suitable Keychain accessibility controls.
* Use iOS Data Protection for sensitive files.
* Store sensitive files inside application-private locations.
* Avoid unnecessary external sharing.
* Clear sensitive cached data when no longer required.

---

# 6. Keychain Security

The iOS Keychain provides protected storage for credentials and cryptographic material.

### Common Issues

* Sensitive information not stored in Keychain
* Weak Keychain accessibility configuration
* Excessive Keychain sharing
* Insecure access groups
* Authentication material remaining after account deletion
* Improper access-control configuration

### Test

Look for Keychain operations such as:

```text
SecItemAdd
SecItemCopyMatching
SecItemUpdate
SecItemDelete
```

Investigate:

```text
kSecAttrAccessible
kSecAttrAccessGroup
kSecClass
```

### Remediation

* Store appropriate authentication secrets in Keychain.
* Select Keychain accessibility based on the required security model.
* Use access controls for highly sensitive credentials.
* Minimize Keychain sharing between applications/extensions.
* Delete credentials when they are no longer required.
* Avoid unnecessarily broad access groups.
* Use hardware-backed protection where supported and appropriate.

---

# 7. Data Protection and File Security

iOS provides Data Protection mechanisms for protecting application files.

### Test

Inspect sensitive files and determine:

* Whether protection is enabled
* When files are accessible
* Whether sensitive data is protected while the device is locked
* Whether credentials remain in unprotected files

### Remediation

* Apply appropriate iOS Data Protection classes to sensitive files.
* Keep highly sensitive information unavailable while the device is locked when the application's requirements permit.
* Minimize plaintext sensitive data on disk.
* Protect temporary files and caches.
* Delete sensitive files when no longer required.

---

# 8. Cryptography

### Common Issues

* Weak algorithms
* Hardcoded encryption keys
* Static IVs
* Weak random numbers
* Deprecated hashes
* ECB mode
* Custom cryptography
* Insecure key management

### Search For

```text
CCCrypt
CryptoKit
CommonCrypto
SecKey
SecRandomCopyBytes
```

### Remediation

* Use Apple-provided cryptographic APIs or well-reviewed libraries.
* Prefer authenticated encryption where appropriate.
* Generate cryptographic keys securely.
* Never hardcode secret keys in the application.
* Use secure random number generation.
* Protect keys using Keychain/appropriate platform facilities.
* Avoid custom cryptographic implementations.

---

# 9. Network Security

### Test

* HTTPS
* TLS versions
* Certificate validation
* Hostname validation
* Sensitive data transmission
* Authentication headers
* Cookies
* Redirects
* API endpoints
* Certificate pinning

### Common Problems

```text
HTTP communication
Weak TLS configuration
Improper certificate validation
Sensitive tokens in URLs
Insecure redirects
```

### Remediation

* Use HTTPS for sensitive communication.
* Validate certificate chains and hostnames.
* Keep networking frameworks updated.
* Do not transmit credentials through URLs.
* Reject invalid TLS certificates.
* Use secure redirect handling.
* Minimize sensitive data transmitted over the network.

---

# 10. App Transport Security

**App Transport Security (ATS)** helps enforce secure network communication.

### Test

Inspect application configuration for:

```text
NSAppTransportSecurity
NSAllowsArbitraryLoads
NSExceptionDomains
```

### Potential Issues

```xml
<key>NSAllowsArbitraryLoads</key>
<true/>
```

Broad ATS exceptions can weaken transport security.

### Remediation

* Keep ATS enabled.
* Avoid global `NSAllowsArbitraryLoads` exceptions.
* Use narrowly scoped exceptions only when technically necessary.
* Require HTTPS for application traffic.
* Document and review every ATS exception.

---

# 11. SSL/TLS Certificate Validation

### Test

Determine whether the application:

* Validates certificate chains
* Validates hostnames
* Rejects expired certificates
* Rejects invalid certificates
* Correctly handles certificate errors

### Common Issue

An application may accept an invalid certificate due to insecure custom validation.

### Remediation

* Use Apple's standard TLS validation.
* Do not blindly accept certificate errors.
* Avoid custom trust evaluation unless necessary.
* Properly validate certificate chains and hostnames.
* Test failure cases during security testing.

---

# 12. Certificate Pinning

Certificate pinning can provide an additional layer of defense against certain network interception scenarios.

### Test

Determine:

```text
Is pinning implemented?
Is it implemented correctly?
Are backup pins configured?
Does certificate rotation work?
Does failure handling fail securely?
```

### Important

> The ability to bypass certificate pinning during authorized testing is **not automatically a vulnerability**.

### Remediation

* Implement pinning only when justified by the threat model.
* Maintain safe certificate rotation procedures.
* Avoid brittle pinning implementations.
* Fail closed when certificate validation fails.
* Never replace proper TLS validation with pinning alone.

---

# 13. URL Schemes

Custom URL schemes allow applications to respond to URLs such as:

```text
myapp://profile
myapp://payment
myapp://reset
```

### Common Issues

* URL scheme hijacking
* Sensitive information exposure
* Authentication bypass
* Parameter manipulation
* Open redirect
* Unauthorized functionality

### Test

* Identify registered schemes.
* Identify accepted parameters.
* Test sensitive functionality.
* Test authentication requirements.
* Test malicious parameter values.

### Remediation

* Prefer Universal Links for security-sensitive web-to-app navigation.
* Do not place sensitive secrets in custom URLs.
* Validate all parameters.
* Require authentication and authorization.
* Do not trust the application that invoked the URL.

---

# 14. Universal Links

Universal Links associate HTTPS URLs with an iOS application.

### Test

Check:

```text
Associated Domains entitlement
apple-app-site-association
URL paths
Application handling
Authentication
Authorization
```

### Common Issues

* Incorrect domain association
* Overly broad URL matching
* Sensitive functionality accessible without authentication
* Parameter manipulation
* Improper URL validation

### Remediation

* Properly configure the `apple-app-site-association` file.
* Restrict application paths to those actually required.
* Validate URL parameters.
* Enforce authentication and authorization server-side.
* Avoid putting sensitive credentials into Universal Link URLs.

---

# 15. WebView Security

iOS applications commonly use `WKWebView`.

### Test

Search for:

```text
WKWebView
WKNavigationDelegate
WKUIDelegate
load(_:)
loadHTMLString()
evaluateJavaScript()
```

### Test

* Untrusted URL loading
* JavaScript
* Navigation
* Local file access
* Sensitive information
* Cookie handling
* SSL/TLS validation
* Cross-origin behavior

### Remediation

* Restrict navigation to trusted origins.
* Validate URLs before loading them.
* Avoid loading attacker-controlled content in privileged WebViews.
* Minimize JavaScript usage.
* Protect sensitive cookies and authentication state.
* Avoid insecure custom navigation handlers.
* Keep WebView dependencies and iOS versions updated.

---

# 16. JavaScript Bridge Security

Applications may expose native functionality to JavaScript.

### Common Risks

```text
JavaScript → Native functionality
```

If untrusted web content can invoke sensitive native functionality, this may create a serious security boundary violation.

### Test

* Native methods exposed to JavaScript
* Authentication requirements
* Input validation
* Origin validation
* Sensitive functionality
* Untrusted page access

### Remediation

* Expose only the minimum required native functionality.
* Validate the origin of requests.
* Validate all JavaScript-supplied parameters.
* Never expose privileged operations to arbitrary web content.
* Require authentication and authorization for sensitive actions.

---

# 17. App Extensions

iOS supports extensions such as:

```text
Share Extensions
Today Widgets
Action Extensions
Keyboard Extensions
Notification Extensions
```

### Test

* Shared data
* App Groups
* Keychain sharing
* Extension permissions
* Sensitive information
* IPC
* Authentication state
* Input validation

### Remediation

* Minimize data shared through App Groups.
* Restrict extension functionality.
* Protect sensitive shared data.
* Use appropriate access controls.
* Avoid storing unnecessary credentials in shared containers.
* Validate all data received by extensions.

---

# 18. Entitlements

Entitlements define special capabilities granted to an application.

### Examples

```text
Keychain Access Groups
Associated Domains
App Groups
Push Notifications
iCloud
Apple Pay
HealthKit
Network Extensions
```

### Test

Determine whether the application has:

* Excessive entitlements
* Unnecessary App Groups
* Broad Keychain access groups
* Unnecessary capabilities
* Sensitive capabilities without appropriate controls

### Remediation

* Apply least privilege.
* Remove unused entitlements.
* Restrict Keychain access groups.
* Minimize App Group sharing.
* Review entitlements for every release.
* Ensure capabilities are justified by application functionality.

---

# 19. Inter-Process Communication

iOS applications and extensions can communicate through several mechanisms.

### Areas

```text
URL Schemes
Universal Links
App Groups
Pasteboard
Extensions
XPC-related mechanisms
Shared containers
```

### Test

* Data exposure
* Input validation
* Authorization
* Trust boundaries
* Sensitive information

### Remediation

* Treat external input as untrusted.
* Authenticate sensitive operations.
* Minimize shared data.
* Validate all IPC parameters.
* Restrict application groups and shared resources.
* Avoid transmitting unnecessary sensitive information.

---

# 20. Pasteboard Security

The iOS pasteboard can expose sensitive information.

### Test

Search for:

```text
UIPasteboard
generalPasteboard
setString
string
setData
```

Check whether the application copies:

```text
Passwords
OTP
Tokens
Account information
Payment information
Sensitive URLs
```

### Remediation

* Avoid copying sensitive information to the general pasteboard.
* Use appropriate pasteboard controls where sensitive copying is necessary.
* Clear temporary sensitive clipboard data when appropriate.
* Inform users when sensitive information is copied where appropriate.

---

# 21. Screenshot and Snapshot Leakage

iOS may capture application snapshots when applications move into the background.

### Test

Check whether sensitive screens remain visible in:

```text
App switcher
Background snapshots
Screen recording
Screenshots
```

### Sensitive Screens

```text
Banking
Passwords
OTP
Payment details
Personal information
Authentication tokens
```

### Remediation

* Obscure sensitive content when the application enters the background where appropriate.
* Design sensitive screens to minimize information leakage.
* Do not rely solely on screenshot prevention for security.
* Protect sensitive information through appropriate application design.

---

# 22. Logging and Sensitive Information

### Test

Search runtime logs for:

```text
Tokens
Passwords
API keys
PII
Session IDs
Payment data
Debug information
Internal URLs
```

Potential sources include:

```text
print()
NSLog
os_log
Logger
Third-party analytics
Crash reporting
```

### Remediation

* Remove sensitive data from production logs.
* Use appropriate logging levels.
* Avoid logging authentication headers or tokens.
* Review third-party logging/crash-reporting configuration.
* Sanitize sensitive information before logging.

---

# 23. SQLite and Core Data

Applications may store data using:

```text
SQLite
Core Data
Realm
Other local databases
```

### Test

Look for:

```text
Authentication tokens
PII
Payment information
User data
Session information
Cached responses
```

### Remediation

* Minimize sensitive data stored locally.
* Apply appropriate file protection.
* Encrypt sensitive databases where justified.
* Protect encryption keys using Keychain/appropriate platform facilities.
* Delete stale data.
* Avoid storing authentication secrets unnecessarily.

---

# 24. Backup and Data Exposure

### Test

Determine whether sensitive application information may be included in:

```text
Device backups
iCloud-related storage
Application data
Shared containers
```

### Sensitive Data

```text
Tokens
Credentials
PII
Databases
Application files
Configuration
```

### Remediation

* Review backup behavior for sensitive data.
* Avoid unnecessary persistent storage.
* Use appropriate iOS data protection mechanisms.
* Remove sensitive temporary data.
* Define a clear backup and restore security model.

---

# 25. File Protection

Sensitive application files should use appropriate iOS Data Protection.

### Test

Identify:

```text
Documents
Library
Caches
Temporary files
SQLite databases
Configuration files
```

Determine when the data is accessible relative to the device lock state.

### Remediation

* Apply appropriate Data Protection classes.
* Protect sensitive files while the device is locked when required.
* Avoid storing secrets in unprotected files.
* Remove temporary sensitive files.
* Protect caches and local databases appropriately.

---

# 26. Biometric Authentication

iOS applications may use:

```text
LocalAuthentication
LAContext
Face ID
Touch ID
```

### Common Issues

* Client-side-only authentication
* Weak fallback authentication
* Authentication state manipulation
* Sensitive operations not properly protected
* Biometric result trusted without appropriate security binding

### Remediation

* Use Apple's LocalAuthentication APIs.
* Use Keychain access controls for sensitive secrets where appropriate.
* Bind sensitive operations to appropriate authentication state.
* Do not rely on a local Boolean as proof of authorization.
* Require server-side authorization for backend-sensitive operations.
* Secure fallback authentication mechanisms.

---

# 27. Jailbreak Detection

Applications may attempt to detect jailbroken devices.

### Test

Identify:

```text
Jailbreak checks
File checks
URL scheme checks
Sandbox checks
Dynamic library checks
Integrity checks
```

### Important

A jailbreak-detection bypass is generally a **testing technique**, not automatically a vulnerability.

### Remediation

* Treat jailbreak detection as defense-in-depth.
* Do not rely on it as the only authorization mechanism.
* Protect sensitive backend operations server-side.
* Use platform integrity mechanisms where appropriate.
* Apply additional risk controls for high-value operations.

---

# 28. Runtime Tampering

### Test

Assess application behavior under authorized runtime instrumentation.

Possible areas:

```text
Method manipulation
Runtime values
Authentication logic
Authorization checks
Cryptographic operations
Security controls
```

### Questions

```text
Can security-sensitive logic be modified?
Does modifying it bypass authorization?
Does it expose sensitive information?
Does the backend independently enforce the control?
```

### Remediation

* Keep security-critical authorization server-side.
* Use runtime integrity mechanisms as defense-in-depth.
* Protect sensitive cryptographic keys.
* Use appropriate code-signing and platform integrity controls.
* Avoid trusting security decisions made entirely inside the client.

---

# 29. Debugging and Binary Protection

### Test

Inspect the application binary for:

```text
Debug configuration
Symbols
Sensitive strings
Hardcoded secrets
Internal endpoints
Debug functions
Test credentials
Unused functionality
```

iOS applications commonly use the **Mach-O** binary format.

### Remediation

* Use production release builds.
* Remove debugging functionality.
* Remove test credentials and development endpoints.
* Strip unnecessary symbols from production builds.
* Protect secrets outside the application.
* Use appropriate compiler and binary-hardening settings.
* Keep sensitive server-side logic outside the mobile binary.

> Obfuscation makes analysis harder but does not make embedded secrets secure.

---

# 30. Privacy and Permission Issues

iOS applications can request access to sensitive device capabilities.

### Areas

```text
Camera
Microphone
Location
Contacts
Photos
Bluetooth
Calendar
Health data
Motion data
```

### Test

* Is the permission necessary?
* Is excessive data collected?
* Is sensitive data transmitted?
* Is permission requested at the appropriate time?
* Is data shared with third parties?

### Remediation

* Follow least-privilege principles.
* Request only required permissions.
* Explain why permissions are required.
* Minimize personal-data collection.
* Secure collected information.
* Review third-party SDK data collection.

---

# 31. Location Data Exposure

Location information is highly sensitive.

### Test

Look for:

```text
GPS coordinates
Location history
Geofencing data
Nearby locations
Location in API requests
Location in logs
Location in analytics
```

### Remediation

* Collect only the precision necessary.
* Request location access only when needed.
* Avoid storing unnecessary location history.
* Encrypt sensitive location data.
* Minimize transmission to third parties.
* Do not log precise location unnecessarily.

---

# 32. Notification Leakage

Push notifications can expose sensitive information.

### Test

Check notifications containing:

```text
OTP
Transaction details
Account information
Password reset information
Personal information
Payment details
```

### Remediation

* Avoid placing sensitive information directly in notification content.
* Use generic notification messages for sensitive events.
* Fetch sensitive details securely after the application opens.
* Consider device lock-screen exposure in the threat model.

---

# 33. Memory and Sensitive Data Handling

Sensitive data may remain in application memory longer than necessary.

### Test

Look for:

```text
Passwords
Tokens
Encryption keys
PII
Payment information
```

stored or retained unnecessarily.

### Remediation

* Minimize the lifetime of sensitive data in memory.
* Avoid unnecessary copies of sensitive strings/data.
* Use appropriate secure handling techniques where available.
* Clear sensitive temporary data where practical.
* Avoid exposing sensitive information through debugging or diagnostic interfaces.

---

# 34. API Security

iOS applications frequently depend heavily on backend APIs.

### Test

```text
Authentication
Authorization
IDOR/BOLA
Mass assignment
Rate limiting
Input validation
Parameter tampering
Business logic
Sensitive data exposure
HTTP methods
Error handling
Race conditions
```

### Example

```http
GET /api/users/1001
```

Test whether another authenticated user can access:

```http
GET /api/users/1002
```

### Remediation

* Authenticate every protected API request.
* Implement object-level authorization.
* Implement function-level authorization.
* Validate all parameters server-side.
* Use explicit request models/allowlists.
* Prevent mass assignment.
* Apply rate limiting.
* Return only required data.
* Implement secure business logic on the server.
* Never assume requests came from a trusted iOS application.

---

# 35. Business Logic

Business logic vulnerabilities occur when an attacker can manipulate a legitimate workflow.

### Test

```text
Payment
Refund
Coupon
Transfer
Purchase
Account upgrade
Password change
Email change
Account deletion
```

### Examples

```text
Price manipulation
Quantity manipulation
Workflow bypass
Replay
Race conditions
Unauthorized state changes
Transaction manipulation
```

### Remediation

* Validate every business operation server-side.
* Enforce valid state transitions.
* Recalculate prices and security-sensitive values server-side.
* Use transaction controls for financial operations.
* Prevent replay where operations must be unique.
* Implement concurrency controls for race-sensitive operations.
* Require authorization for every state-changing operation.

---

# 36. Account Recovery

### Test

* Password reset
* Email change
* Phone-number change
* Recovery token
* OTP
* Token expiration
* Token reuse
* Account enumeration
* Recovery authorization

### Remediation

* Generate high-entropy recovery tokens.
* Make recovery tokens short-lived and single-use.
* Bind recovery challenges to the correct account.
* Apply rate limiting.
* Revoke appropriate sessions after credential recovery.
* Avoid account enumeration.
* Perform recovery authorization server-side.

---

# 37. Account Deletion

### Test

```text
Login
 ↓
Capture session
 ↓
Delete account
 ↓
Replay session
 ↓
Access API
```

Also test:

```text
Local cached data
Keychain data
Database
Cookies
Tokens
Shared containers
```

### Remediation

* Revoke active sessions.
* Revoke refresh tokens.
* Ensure deleted accounts cannot authenticate.
* Remove or appropriately retain personal data according to requirements.
* Clear local sensitive account data.
* Verify API authorization checks account status.
* Test re-registration and account recovery edge cases.

---

# 38. Client-Side Security

The iOS application should be considered an **untrusted client**.

### Test

Look for:

```text
isAdmin
isAuthenticated
premium=true
verified=true
role=user
price=
balance=
```

### Common Issues

* Client-side authorization
* Client-controlled prices
* Client-controlled roles
* Client-controlled account state
* Hidden security functionality
* Local-only validation

### Remediation

* Perform authorization server-side.
* Never trust client-controlled security flags.
* Recalculate financial values on the server.
* Validate roles and permissions server-side.
* Treat all mobile input as untrusted.

---

# 39. Third-Party SDK Security

Modern iOS applications frequently include third-party SDKs.

Examples:

```text
Analytics
Crash reporting
Advertising
Payments
Authentication
Social login
Maps
Push notifications
```

### Test

Identify:

```text
SDK versions
Data collected
Network destinations
Permissions
Embedded secrets
Logging
Privacy impact
```

### Remediation

* Keep third-party dependencies updated.
* Remove unused SDKs.
* Review SDK permissions and data collection.
* Monitor third-party network communication.
* Avoid SDKs with unnecessary access to sensitive information.
* Maintain an inventory of third-party dependencies.
* Review security advisories for critical dependencies.

---

# 40. Testing Techniques vs Findings

This distinction is extremely important.

| Observation / Technique                   | Automatically a Vulnerability? |
| ----------------------------------------- | ------------------------------ |
| IPA can be analyzed                       | ❌ No                           |
| Mach-O can be inspected                   | ❌ No                           |
| Strings can be extracted                  | ❌ No                           |
| Runtime methods can be observed           | ❌ No                           |
| Jailbreak detection can be bypassed       | ❌ No                           |
| Certificate pinning can be bypassed       | ❌ No                           |
| Custom URL scheme exists                  | ❌ No                           |
| Keychain is used                          | ❌ No                           |
| Sensitive token stored insecurely         | ✅ Potential finding            |
| Invalid TLS certificate accepted          | ✅ Finding                      |
| Unauthorized API access                   | ✅ Finding                      |
| Sensitive exported functionality          | ✅ Potential finding            |
| Authentication bypass                     | ✅ Finding                      |
| Authorization bypass                      | ✅ Finding                      |
| Sensitive data exposed through pasteboard | ✅ Potential finding            |
| Excessive entitlement                     | ⚠️ Context dependent           |

### Golden Rule

> **A testing technique is not necessarily a vulnerability. Validate the actual security impact.**

For example:

```text
Jailbreak detection bypass
        ↓
Does it bypass authorization?
        ↓
Does it expose protected data?
        ↓
Does it enable unauthorized transactions?
```

If not, the bypass itself may not constitute a reportable vulnerability.

---

# 41. Evidence Collection

For every confirmed finding, collect:

```text
Title
Severity
CVSS
Affected application/version
Affected component
Description
Impact
Steps to reproduce
Screenshots
Requests/responses
Relevant code
Configuration
PoC
Remediation
Retest result
```

### Evidence Workflow

```text
Observation
     ↓
Reproduction
     ↓
Proof of Impact
     ↓
Evidence
     ↓
Report
     ↓
Remediation
     ↓
Retest
```

Avoid collecting unnecessary personal or production data.

---

# 42. Final Checklist

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

## 👤 Authorization

* [ ] IDOR/BOLA
* [ ] Horizontal privilege escalation
* [ ] Vertical privilege escalation
* [ ] Function-level authorization
* [ ] Object-level authorization
* [ ] Client-side authorization

## 🔑 Sessions & Tokens

* [ ] Session expiration
* [ ] Logout invalidation
* [ ] JWT validation
* [ ] Token expiration
* [ ] Refresh tokens
* [ ] Token reuse
* [ ] Session fixation
* [ ] Account deletion invalidation

## 🔐 Keychain

* [ ] Token storage
* [ ] Password storage
* [ ] Keychain accessibility
* [ ] Access groups
* [ ] Shared Keychain
* [ ] Keychain cleanup

## 💾 Local Storage

* [ ] UserDefaults
* [ ] SQLite
* [ ] Core Data
* [ ] Files
* [ ] Cache
* [ ] Cookies
* [ ] WebView storage
* [ ] Logs
* [ ] Pasteboard

## 🔒 Cryptography

* [ ] Algorithms
* [ ] Encryption keys
* [ ] IV/nonce
* [ ] Randomness
* [ ] Hashing
* [ ] Key management
* [ ] Key rotation

## 🌐 Network

* [ ] HTTPS
* [ ] TLS validation
* [ ] Hostname validation
* [ ] ATS
* [ ] ATS exceptions
* [ ] Certificate pinning
* [ ] Sensitive data transmission
* [ ] Redirects

## 🔗 URL Handling

* [ ] Custom URL schemes
* [ ] URL scheme hijacking
* [ ] Universal Links
* [ ] Associated Domains
* [ ] URI validation
* [ ] Parameter manipulation
* [ ] Token leakage

## 🌍 WebView

* [ ] WKWebView
* [ ] JavaScript
* [ ] Navigation
* [ ] URL validation
* [ ] JavaScript bridges
* [ ] Cookies
* [ ] Untrusted content
* [ ] SSL handling

## 📦 iOS Platform

* [ ] Entitlements
* [ ] App Groups
* [ ] App Extensions
* [ ] IPC
* [ ] Pasteboard
* [ ] File Protection
* [ ] Backup
* [ ] Notifications
* [ ] Screenshots

## 🛡️ Runtime

* [ ] Jailbreak detection
* [ ] Debugger detection
* [ ] Runtime tampering
* [ ] Integrity checks
* [ ] Binary protection
* [ ] Sensitive runtime data

## 🔌 API

* [ ] Authentication
* [ ] Authorization
* [ ] IDOR/BOLA
* [ ] Mass assignment
* [ ] Rate limiting
* [ ] Parameter tampering
* [ ] Business logic
* [ ] Sensitive data exposure
* [ ] Race conditions

## 🔒 Privacy

* [ ] Permissions
* [ ] Location
* [ ] Camera
* [ ] Microphone
* [ ] Contacts
* [ ] Photos
* [ ] Analytics
* [ ] Crash reporting
* [ ] Third-party SDKs
* [ ] Notification leakage

## 📝 Reporting

* [ ] Vulnerability validated
* [ ] Impact confirmed
* [ ] Evidence collected
* [ ] Severity assessed
* [ ] Remediation provided
* [ ] Retest completed

---

# 🧰 Recommended iOS Security Tools

| Tool           | Purpose                                |
| -------------- | -------------------------------------- |
| **Frida**      | Runtime instrumentation                |
| **Objection**  | Runtime mobile security testing        |
| **Burp Suite** | HTTP/API interception                  |
| **MobSF**      | Automated mobile security analysis     |
| **Ghidra**     | Native binary analysis                 |
| **Hopper**     | Mach-O disassembly/reverse engineering |
| **LLDB**       | Debugging and runtime analysis         |
| **class-dump** | Objective-C class inspection           |
| **otool**      | Mach-O inspection                      |
| **strings**    | String extraction                      |
| **codesign**   | Code-signing inspection                |
| **PlistBuddy** | Property-list inspection               |
| **simctl**     | iOS Simulator management               |
| **Xcode**      | Build, debug, and simulator testing    |

---

# 🎯 iOS Security Testing Model

A complete iOS penetration test should combine:

```text
                 iOS Application
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
   Static Analysis            Dynamic Analysis
          │                         │
          ▼                         ▼
   IPA / Mach-O              Runtime Behavior
   Info.plist                Frida / LLDB
   Entitlements              Application State
   Strings                   Security Controls
   Classes
          │                         │
          └────────────┬────────────┘
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
          Authentication / Authorization
                       │
                       ▼
                Business Logic
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

# 📚 Recommended Repository Structure

```text
mobile/
│
├── android/
│   ├── mobile-testing-workflow.md
│   └── vulnerabilities.md
│
└── ios/
    ├── ios-testing-workflow.md
    ├── vulnerabilities.md
    │
    ├── static-analysis/
    │   ├── mobsf.md
    │   ├── ghidra.md
    │   ├── hopper.md
    │   └── macho.md
    │
    ├── dynamic-analysis/
    │   ├── frida.md
    │   ├── objection.md
    │   └── lldb.md
    │
    ├── network/
    │   └── burp-suite.md
    │
    └── labs/
```

---

# 🎯 Final Principle

A professional iOS security assessment should not be limited to extracting the IPA or bypassing jailbreak detection.

The complete assessment should cover:

```text
Static Analysis
      +
Dynamic Analysis
      +
Keychain Security
      +
Data Protection
      +
Network Security
      +
URL / Universal Links
      +
WebView Security
      +
Entitlements
      +
App Extensions
      +
Authentication
      +
Authorization
      +
API Security
      +
Business Logic
      +
Privacy
      +
Vulnerability Validation
      +
Remediation Verification
```

The objective is to identify **real security weaknesses and demonstrate their security impact**, rather than simply identifying implementation details that look unusual.

> **Understand → Analyze → Test → Validate → Demonstrate Impact → Report → Remediate → Retest**
