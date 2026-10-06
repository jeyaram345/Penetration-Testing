# 📱Testing Workflow

A practical workflow for conducting **Android mobile application security assessments**.

This workflow combines:

* Application reconnaissance
* APK analysis
* Static analysis
* Dynamic analysis
* Network/API testing
* Android component testing
* Authentication and authorization testing
* Business logic testing
* Runtime instrumentation
* Vulnerability validation
* Reporting
* Remediation verification

> ⚠️ **Disclaimer:** Perform testing only on applications, devices, APIs, and environments for which you have explicit authorization.

---

# 📑 Table of Contents

* [1. Scope and Authorization](#1-scope-and-authorization)
* [2. Test Environment Setup](#2-test-environment-setup)
* [3. Application Reconnaissance](#3-application-reconnaissance)
* [4. APK Acquisition](#4-apk-acquisition)
* [5. APK Fingerprinting](#5-apk-fingerprinting)
* [6. Static Analysis](#6-static-analysis)
* [7. Manifest Analysis](#7-manifest-analysis)
* [8. Code and Secret Analysis](#8-code-and-secret-analysis)
* [9. Android Component Testing](#9-android-component-testing)
* [10. Deep Link Testing](#10-deep-link-testing)
* [11. WebView Testing](#11-webview-testing)
* [12. Local Storage Testing](#12-local-storage-testing)
* [13. Cryptography Analysis](#13-cryptography-analysis)
* [14. Network Security Testing](#14-network-security-testing)
* [15. Dynamic Analysis](#15-dynamic-analysis)
* [16. Authentication Testing](#16-authentication-testing)
* [17. Authorization Testing](#17-authorization-testing)
* [18. Session and Token Testing](#18-session-and-token-testing)
* [19. API Security Testing](#19-api-security-testing)
* [20. Business Logic Testing](#20-business-logic-testing)
* [21. Runtime Instrumentation](#21-runtime-instrumentation)
* [22. Root and Anti-Tampering Testing](#22-root-and-anti-tampering-testing)
* [23. File and Backup Testing](#23-file-and-backup-testing)
* [24. Privacy Testing](#24-privacy-testing)
* [25. Vulnerability Validation](#25-vulnerability-validation)
* [26. Evidence Collection](#26-evidence-collection)
* [27. Reporting](#27-reporting)
* [28. Remediation Verification](#28-remediation-verification)
* [29. Final Checklist](#29-final-checklist)
* [30. Recommended Toolset](#30-recommended-toolset)
* [31. Complete Workflow](#31-complete-workflow)

---

# 1. Scope and Authorization

Before performing any testing, define the assessment scope.

## Confirm

```text
Application:
Package Name:
Version:
Environment:
API Domains:
Test Accounts:
Testing Dates:
Allowed Devices:
Allowed Techniques:
Out-of-Scope Components:
```

## Determine

* Black-box / grey-box / white-box
* Production / staging / UAT
* Android versions
* Supported architectures
* API endpoints
* Authentication mechanisms
* Test accounts
* Administrative accounts
* Third-party integrations

### Scope Example

```text
Application:
    Example Banking App

Package:
    com.example.bank

Environment:
    UAT

API:
    https://api-uat.example.com

Accounts:
    User A
    User B
    Admin Test Account

Testing:
    Authorized security assessment
```

> Never begin active testing against an application or API without confirming authorization and scope.

---

# 2. Test Environment Setup

Prepare an isolated Android testing environment.

## Recommended Environment

```text
Windows / Linux
      │
      ├── Android SDK
      ├── Android Emulator / Test Device
      ├── ADB
      ├── Burp Suite
      ├── JADX
      ├── APKTool
      ├── MobSF
      ├── Frida
      ├── Objection
      └── Ghidra
```

## Verify ADB

```bash
adb devices
```

Expected:

```text
List of devices attached
emulator-5554    device
```

## Check Android Information

```bash
adb shell getprop ro.build.version.release
adb shell getprop ro.product.cpu.abi
```

## Check Package

```bash
adb shell pm list packages
```

---

# 3. Application Reconnaissance

Start by understanding the application's functionality.

## Identify

* Application name
* Package name
* Version
* Developer
* Main functionality
* Authentication mechanism
* API endpoints
* Payment functionality
* Account functionality
* File upload/download
* Deep links
* WebViews
* Third-party integrations
* Push notifications
* Biometric authentication
* Location functionality

## Functional Mapping

Create a simple application map:

```text
Application
│
├── Registration
├── Login
├── MFA / OTP
├── Home
├── Profile
├── Payments
├── Transactions
├── Notifications
├── Settings
├── Password Change
├── Account Recovery
└── Account Deletion
```

This becomes the basis for later security testing.

---

# 4. APK Acquisition

Obtain the APK through an authorized source.

Possible sources:

```text
Internal testing build
Play Store test deployment
Developer-provided APK
Authorized test environment
MDM/test distribution
```

## Pull APK from Device

Find package:

```bash
adb shell pm path com.example.app
```

Example:

```text
package:/data/app/~~xxxxx==/com.example.app-xxxxx==/base.apk
```

Pull it:

```bash
adb pull /path/to/base.apk
```

---

# 5. APK Fingerprinting

Before analysis, record basic APK information.

## Package Information

```bash
aapt dump badging app.apk
```

Look for:

```text
package
versionCode
versionName
minSdkVersion
targetSdkVersion
uses-permission
launchable-activity
```

## APK Signature

```bash
apksigner verify --verbose app.apk
```

## File Hash

```bash
sha256sum app.apk
```

Windows:

```powershell
Get-FileHash app.apk -Algorithm SHA256
```

Record:

```text
Package:
Version:
SHA-256:
Min SDK:
Target SDK:
Architecture:
Signature:
```

---

# 6. Static Analysis

Static analysis examines the APK without executing it.

## Primary Tools

```text
JADX
APKTool
MobSF
Ghidra
```

## Recommended Flow

```text
APK
 │
 ├── MobSF
 │
 ├── JADX
 │    ├── Java/Kotlin
 │    ├── API endpoints
 │    ├── Secrets
 │    ├── Authentication
 │    ├── WebViews
 │    └── Security controls
 │
 ├── APKTool
 │    ├── AndroidManifest.xml
 │    ├── Resources
 │    └── Smali
 │
 └── Ghidra
      └── Native libraries
```

---

# 7. Manifest Analysis

Extract the manifest:

```bash
apktool d app.apk -o app_decoded
```

Open:

```text
AndroidManifest.xml
```

## Check

### Activities

```xml
<activity
    android:name=".ExampleActivity"
    android:exported="true" />
```

### Services

```xml
<service
    android:name=".ExampleService"
    android:exported="true" />
```

### Receivers

```xml
<receiver
    android:name=".ExampleReceiver"
    android:exported="true" />
```

### Providers

```xml
<provider
    android:name=".ExampleProvider"
    android:exported="true" />
```

### Other Items

Check:

```text
Permissions
Exported components
Intent filters
Deep links
Backup configuration
Debuggable flag
Network Security Configuration
FileProvider
Queries
Custom permissions
```

---

# 8. Code and Secret Analysis

Open the APK in JADX.

Search for:

```text
password
passwd
secret
token
apikey
api_key
authorization
bearer
client_secret
private_key
firebase
aws
username
admin
debug
test
staging
```

## Search for Network Code

```text
Retrofit
OkHttp
HttpURLConnection
URLConnection
WebView
loadUrl
baseUrl
endpoint
GraphQL
WebSocket
```

## Authentication

Search:

```text
login
logout
register
refresh
token
otp
mfa
verify
password
reset
biometric
```

## Storage

Search:

```text
SharedPreferences
SQLiteDatabase
Room
DataStore
getSharedPreferences
openFileOutput
getExternalFilesDir
```

---

# 9. Android Component Testing

Test each externally accessible component.

```text
Activities
Services
Broadcast Receivers
Content Providers
```

## Questions

```text
Is it exported?
       ↓
Can another application invoke it?
       ↓
Does it require permissions?
       ↓
Does it process user-controlled data?
       ↓
Does it expose sensitive functionality?
       ↓
Is authorization enforced?
```

### Important

An exported component is not automatically a vulnerability.

The important question is:

> **Can an unauthorized caller abuse the component to perform a security-sensitive action or access protected information?**

---

# 10. Deep Link Testing

Identify deep links from:

```text
AndroidManifest.xml
JADX
String searches
Application behavior
```

Examples:

```text
example://profile
example://payment
example://reset
https://example.com/app
```

## Test

```text
Authentication bypass
Authorization bypass
Parameter manipulation
Open redirect
Sensitive data exposure
Token leakage
Untrusted URL handling
WebView interaction
```

Example:

```bash
adb shell am start \
-a android.intent.action.VIEW \
-d "example://profile"
```

---

# 11. WebView Testing

Identify:

```java
WebView
loadUrl()
setJavaScriptEnabled()
addJavascriptInterface()
WebViewClient
WebChromeClient
```

## Test

```text
JavaScript
URL validation
Navigation restrictions
File access
Local file access
JavaScript interfaces
SSL handling
Untrusted content
Sensitive information
```

### Security Questions

```text
Can attacker-controlled content be loaded?
Can JavaScript access native functionality?
Can sensitive data reach the WebView?
Can the WebView navigate to arbitrary domains?
```

---

# 12. Local Storage Testing

Inspect:

```text
SharedPreferences
SQLite
Room
DataStore
Files
Cache
External storage
Cookies
WebView storage
Logs
```

## Common Sensitive Data

```text
JWT
Access token
Refresh token
Password
PII
Payment information
API keys
Encryption keys
Session identifiers
```

## Questions

```text
Is sensitive information stored?
       ↓
Is it encrypted?
       ↓
Is the encryption key protected?
       ↓
Can another application access it?
       ↓
Does it remain after logout?
       ↓
Does it remain after account deletion?
```

---

# 13. Cryptography Analysis

Identify cryptographic implementation.

Search:

```text
Cipher
MessageDigest
Mac
SecretKeySpec
KeyGenerator
SecureRandom
IvParameterSpec
KeyStore
```

## Check

```text
Algorithm
Mode
Padding
Key generation
Key storage
IV/nonce generation
Randomness
Hashing
Password derivation
Key lifecycle
```

### Red Flags

```text
Hardcoded keys
Static IV
Weak algorithms
ECB
MD5 for security-sensitive purposes
SHA-1 for security-sensitive purposes
Math.random()
Custom encryption
Keys embedded in APK
```

---

# 14. Network Security Testing

Configure the device/emulator to use an intercepting proxy where authorized.

```text
Android Application
        │
        ▼
    Burp Proxy
        │
        ▼
     Internet/API
```

## Capture

```text
Login
Registration
Profile
Password change
Payments
Transactions
File upload
File download
Account deletion
OTP
Password reset
```

## Test

```text
TLS
Certificate validation
Certificate pinning
HTTP methods
Headers
Tokens
Cookies
Authorization
Parameters
Response data
Error handling
Redirects
```

---

# 15. Dynamic Analysis

Dynamic analysis evaluates application behavior while the application is running.

## Observe

```text
Application behavior
Network requests
Files
Databases
Logs
Processes
Activities
Services
Intents
Runtime values
Security controls
```

## Useful Commands

```bash
adb logcat
```

```bash
adb shell dumpsys package com.example.app
```

```bash
adb shell dumpsys activity activities
```

```bash
adb shell ps
```

```bash
adb shell getprop
```

---

# 16. Authentication Testing

Test the complete authentication lifecycle.

## Login

```text
Valid credentials
Invalid credentials
Empty credentials
Rate limiting
Account enumeration
Brute-force resistance
```

## Registration

```text
Duplicate account
Email verification
Password policy
OTP
Username enumeration
```

## MFA

```text
OTP reuse
OTP expiration
OTP brute force
OTP bypass
MFA state manipulation
```

## Password

```text
Password change
Old password validation
Password reset
Reset token
Session invalidation
```

---

# 17. Authorization Testing

Use at least two test accounts where possible.

```text
User A
User B
Admin
```

## Test

```text
User A → User A resource
User A → User B resource
User A → Admin functionality
User B → User A resource
```

Test:

```text
GET
POST
PUT
PATCH
DELETE
```

Do not rely solely on HTTP status codes.

Verify whether unauthorized data or functionality is actually returned or performed.

---

# 18. Session and Token Testing

Capture authentication tokens using authorized testing infrastructure.

## Test Lifecycle

```text
Login
  ↓
Capture token
  ↓
Use token
  ↓
Logout
  ↓
Replay token
  ↓
Password change
  ↓
Replay token
  ↓
Account deletion
  ↓
Replay token
```

## Check

```text
Expiration
Revocation
Rotation
Reuse
Scope
Audience
Issuer
Session binding
Refresh-token behavior
```

---

# 19. API Security Testing

Mobile applications should be treated as untrusted clients.

## Test API Categories

```text
Authentication
Authorization
User management
Payments
Transactions
Files
Profiles
Notifications
Password management
Account deletion
```

## Test

```text
IDOR / BOLA
Broken authorization
Parameter tampering
Mass assignment
Rate limiting
HTTP method manipulation
Input validation
Business logic
Information disclosure
Excessive data exposure
```

### Example

```http
GET /api/users/1001
```

Test with another authorized test account:

```http
GET /api/users/1002
```

The important question is whether the server properly verifies ownership.

---

# 20. Business Logic Testing

Business logic vulnerabilities cannot always be identified through automated scanning.

Understand the intended workflow first.

## Example

```text
Add Item
   ↓
Apply Coupon
   ↓
Checkout
   ↓
Payment
   ↓
Order Confirmation
```

Test whether the workflow can be manipulated.

### Areas

```text
Price manipulation
Quantity manipulation
Coupon abuse
Workflow bypass
Replay
Race conditions
Transaction manipulation
Unauthorized state transitions
Account ownership
Refund logic
Payment logic
```

---

# 21. Runtime Instrumentation

Use runtime instrumentation in authorized environments to understand application behavior.

Common tools:

```text
Frida
Objection
ADB
```

## Useful Areas

```text
Method behavior
Runtime values
Security controls
Crypto operations
Network configuration
Root detection
Certificate pinning
Application logic
```

### Workflow

```text
Static Analysis
      ↓
Identify interesting method
      ↓
Run application
      ↓
Observe runtime behavior
      ↓
Instrument method
      ↓
Validate security control
      ↓
Determine actual security impact
```

> Runtime instrumentation is a testing technique. A successful hook or bypass does not automatically constitute a vulnerability.

---

# 22. Root and Anti-Tampering Testing

Identify controls such as:

```text
Root detection
Emulator detection
Debugger detection
Frida detection
Integrity checks
Signature checks
Tamper detection
RASP
Play Integrity
```

## Testing Goal

Determine whether bypassing a control exposes a genuine security weakness.

For example:

```text
Root detection bypass
        ↓
Can attacker access sensitive data?
        ↓
Can attacker perform unauthorized transactions?
        ↓
Can attacker bypass authentication?
        ↓
Can attacker access privileged functionality?
```

If the bypass only allows the application to run on a rooted test device, it is not automatically a security finding.

---

# 23. File and Backup Testing

Inspect:

```text
Internal storage
External storage
Cache
Temporary files
Databases
SharedPreferences
Backup configuration
FileProvider
```

## Test

```text
Sensitive files
File permissions
Backup exposure
Restore behavior
File sharing
Temporary files
Deleted data
Cached data
```

Check whether sensitive information survives:

```text
Logout
Account deletion
Application restart
Application reinstall
Backup/restore
```

---

# 24. Privacy Testing

Identify what information the application collects and transmits.

## Check

```text
Location
Contacts
Camera
Microphone
Device identifiers
Advertising identifiers
User profile
Financial information
Analytics
Crash reporting
Clipboard
Notifications
```

## Questions

```text
Is the permission necessary?
Is the data necessary?
Is the data transmitted securely?
Is sensitive information logged?
Is third-party SDK access justified?
Is data retained unnecessarily?
```

---

# 25. Vulnerability Validation

Do not report every unusual behavior as a vulnerability.

Use this validation model:

```text
Observation
     ↓
Reproduce
     ↓
Understand root cause
     ↓
Determine security boundary
     ↓
Demonstrate unauthorized impact
     ↓
Assess severity
     ↓
Document evidence
```

## Example

### Observation

```text
Activity exported = true
```

This is not automatically a vulnerability.

### Validate

```text
Can an external application launch it?
        ↓
Does it expose sensitive functionality?
        ↓
Is authentication required?
        ↓
Is authorization enforced?
        ↓
Can sensitive data be accessed?
```

Only then determine whether there is a reportable finding.

---

# 26. Evidence Collection

For every confirmed vulnerability, collect:

```text
Title
Severity
CVSS
Affected version
Affected component
Description
Impact
Steps to reproduce
Request
Response
Screenshots
Logs
Relevant code
Manifest evidence
PoC
Remediation
Retest result
```

## Good Evidence

```text
Before
 ↓
Attack/Test
 ↓
Result
 ↓
Impact
```

Avoid collecting unnecessary sensitive user information.

---

# 27. Reporting

A strong mobile security report should be reproducible.

## Finding Template

```markdown
# Finding: Insecure Storage of Authentication Token

## Severity

Medium

## Description

The application stores a long-lived authentication token
in unprotected local storage.

## Impact

An attacker with access to the application's local data
may be able to recover the token and potentially access
authenticated functionality.

## Steps to Reproduce

1. Install the application.
2. Authenticate using a test account.
3. Inspect application storage.
4. Locate the authentication token.
5. Verify whether the token provides authenticated access.

## Evidence

[Insert screenshots / code / request]

## Remediation

Store sensitive authentication material using appropriate
platform-protected storage and minimize token lifetime.

## Retest

[Document remediation verification]
```

---

# 28. Remediation Verification

After developers fix an issue, repeat the original test.

## Retesting Workflow

```text
Original Finding
       ↓
Developer Fix
       ↓
Deploy Fixed Build
       ↓
Repeat Original PoC
       ↓
Verify Expected Secure Behavior
       ↓
Regression Testing
       ↓
Close Finding
```

## Example

Original:

```text
Token remains valid after logout
```

Retest:

```text
Login
 ↓
Capture token
 ↓
Logout
 ↓
Replay token
 ↓
Expected: unauthorized / rejected
```

Do not mark a finding fixed solely because source code changed.

---

# 29. Final Checklist

## 📋 Scope

* [ ] Authorization confirmed
* [ ] Application identified
* [ ] Package name identified
* [ ] Version identified
* [ ] API scope identified
* [ ] Test accounts created
* [ ] Out-of-scope items documented

## 🔍 Reconnaissance

* [ ] Application functionality mapped
* [ ] Authentication flow mapped
* [ ] API functionality mapped
* [ ] Deep links identified
* [ ] WebViews identified
* [ ] Third-party integrations identified

## 📦 APK

* [ ] APK acquired
* [ ] SHA-256 calculated
* [ ] Package identified
* [ ] Version identified
* [ ] Min SDK identified
* [ ] Target SDK identified
* [ ] Architecture identified
* [ ] Signature checked

## 🔬 Static Analysis

* [ ] Manifest analyzed
* [ ] Activities analyzed
* [ ] Services analyzed
* [ ] Receivers analyzed
* [ ] Providers analyzed
* [ ] Permissions analyzed
* [ ] Deep links analyzed
* [ ] WebViews analyzed
* [ ] API endpoints identified
* [ ] Secrets searched
* [ ] Authentication logic reviewed
* [ ] Cryptography reviewed
* [ ] Storage reviewed
* [ ] Native libraries reviewed

## 📱 Dynamic Analysis

* [ ] Application behavior observed
* [ ] Logcat reviewed
* [ ] Files inspected
* [ ] Database inspected
* [ ] SharedPreferences inspected
* [ ] Activities tested
* [ ] Intents tested
* [ ] Services tested
* [ ] Providers tested
* [ ] Receivers tested

## 🌐 Network

* [ ] Proxy configured
* [ ] HTTPS tested
* [ ] TLS validation tested
* [ ] Certificate validation tested
* [ ] Authentication requests tested
* [ ] Authorization tested
* [ ] Tokens tested
* [ ] API endpoints tested

## 🔐 Authentication

* [ ] Login
* [ ] Registration
* [ ] Password change
* [ ] Password reset
* [ ] OTP
* [ ] MFA
* [ ] Email verification
* [ ] Account recovery
* [ ] Account deletion

## 👤 Authorization

* [ ] Horizontal authorization
* [ ] Vertical authorization
* [ ] IDOR/BOLA
* [ ] Function-level authorization
* [ ] Object-level authorization
* [ ] Administrative functionality

## 🧠 Business Logic

* [ ] Workflow bypass
* [ ] Parameter manipulation
* [ ] Price manipulation
* [ ] Transaction manipulation
* [ ] Replay
* [ ] Race conditions
* [ ] State manipulation

## 🛡️ Runtime

* [ ] Root detection
* [ ] Emulator detection
* [ ] Debugger detection
* [ ] Frida detection
* [ ] Tamper detection
* [ ] Runtime integrity
* [ ] RASP
* [ ] Play Integrity

## 📝 Reporting

* [ ] Finding validated
* [ ] Impact confirmed
* [ ] Evidence collected
* [ ] CVSS assessed
* [ ] Remediation provided
* [ ] Finding reported
* [ ] Retest completed

---

# 30. Recommended Toolset

| Tool               | Primary Use                          |
| ------------------ | ------------------------------------ |
| **ADB**            | Device interaction and debugging     |
| **JADX**           | Java/Kotlin decompilation            |
| **APKTool**        | APK decoding, resources and Smali    |
| **MobSF**          | Automated mobile security analysis   |
| **Burp Suite**     | HTTP/API interception                |
| **Frida**          | Runtime instrumentation              |
| **Objection**      | Runtime mobile testing               |
| **Ghidra**         | Native library analysis              |
| **Drozer**         | Android IPC/component testing        |
| **apksigner**      | APK signature verification           |
| **zipalign**       | APK alignment                        |
| **Android Studio** | Emulator and application development |
| **SQLite Browser** | SQLite database analysis             |

---

# 31. Complete Workflow

The complete Android mobile penetration testing methodology can be summarized as:

```text
                    ┌──────────────────────┐
                    │  Scope & Authorization│
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Environment Setup    │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Application Recon    │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ APK Acquisition      │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ APK Fingerprinting   │
                    └──────────┬───────────┘
                               │
                    ┌──────────┴───────────┐
                    ▼                      ▼
          ┌──────────────────┐    ┌──────────────────┐
          │ Static Analysis  │    │ Dynamic Analysis │
          └────────┬─────────┘    └────────┬─────────┘
                   │                       │
          ┌────────┴─────────┐    ┌────────┴─────────┐
          │                  │    │                  │
          ▼                  ▼    ▼                  ▼
       JADX              APKTool ADB              Frida
       MobSF             Manifest Burp            Objection
       Ghidra            Smali
          │                  │    │                  │
          └────────┬─────────┘    └────────┬─────────┘
                   │                       │
                   └───────────┬───────────┘
                               ▼
                    ┌──────────────────────┐
                    │ Network / API Testing│
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Authentication       │
                    │ Authorization        │
                    │ Session / Tokens     │
                    │ Business Logic       │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Vulnerability        │
                    │ Validation            │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Evidence Collection  │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Security Report      │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Remediation          │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Retest / Verification│
                    └──────────────────────┘
```

---

# 🎯 Final Principle

A professional mobile penetration test is not simply:

```text
Run MobSF
     +
Open APK in JADX
     +
Run Frida
     +
Find something interesting
```

Instead:

```text
Understand
    ↓
Analyze
    ↓
Test
    ↓
Validate
    ↓
Demonstrate Impact
    ↓
Report
    ↓
Remediate
    ↓
Retest
```

The ultimate objective is to identify **real security weaknesses and their practical impact**, not merely unusual implementation details.

---

## 📚 Recommended Repository Structure

```text
mobile/
│
├── README.md
│
├── mobile-testing-workflow.md
├── mobile-vulnerabilities.md
│
├── static-analysis/
│   ├── jadx.md
│   ├── apktool.md
│   ├── mobsf.md
│   └── ghidra.md
│
├── dynamic-analysis/
│   ├── adb.md
│   ├── frida.md
│   └── objection.md
│
├── network/
│   └── burp-suite.md
│
├── vulnerabilities/
│   ├── authentication.md
│   ├── authorization.md
│   ├── insecure-storage.md
│   ├── deep-links.md
│   ├── exported-components.md
│   ├── webview.md
│   ├── cryptography.md
│   ├── network-security.md
│   ├── intent-injection.md
│   ├── content-providers.md
│   ├── backup.md
│   └── api-security.md
│
└── labs/
    ├── mobilehackinglab/
    ├── injuredandroid/
    ├── diva/
    └── other-labs/
```

This structure separates **workflow, tools, vulnerabilities, and hands-on labs**, making the repository useful both as a personal pentesting reference and as a portfolio for mobile security roles.
