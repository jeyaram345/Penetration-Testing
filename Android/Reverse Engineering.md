# 🔬 Reverse Engineering Techniques

During mobile application penetration testing, it is important to distinguish between:

### Testing Technique

A method used to investigate or validate application behavior.

Examples:

```text
JADX analysis
Smali analysis
Frida hooking
APK patching
Burp interception
Native library analysis
```

### Security Finding

A security weakness discovered through those techniques.

Examples:

```text
Hardcoded API Secret
Insecure Data Storage
Broken Access Control
Improper Certificate Validation
Exported Sensitive Activity
Weak Cryptography
```

> **Important:** A testing technique is not automatically a vulnerability. For example, bypassing SSL pinning with Frida is generally a testing technique used to inspect HTTPS traffic; the actual finding would depend on what security weakness is discovered after inspection.

---

# 🧪 Part 1 — Mobile Reverse Engineering Testing Techniques

## 1. APK Information Gathering

### Objective

Understand the application before performing deeper analysis.

### Tools

```text
ADB
Apktool
MobSF
aapt / aapt2
apkanalyzer
```

### Things to collect

```text
Package name
Version
Min SDK
Target SDK
Supported ABIs
Permissions
Activities
Services
Receivers
Providers
Intent Filters
Signing information
Native libraries
```

Example:

```bash
adb shell dumpsys package com.example.app
```

---

# 2. AndroidManifest Analysis

Inspect:

```text
AndroidManifest.xml
```

Look for:

```text
Exported components
Permissions
Intent filters
Deep links
App links
Debuggable
Backup configuration
Network security configuration
Custom permissions
```

Example:

```xml
<activity
    android:name=".AdminActivity"
    android:exported="true">
</activity>
```

This is **not automatically a vulnerability**.

You need to determine whether the exported component exposes sensitive functionality without appropriate authorization.

---

# 3. DEX Decompilation

Use JADX:

```bash
jadx-gui app.apk
```

Analyze:

```text
classes.dex
classes2.dex
classes3.dex
```

Search for:

```text
API endpoints
Authentication
Authorization
Tokens
Secrets
Cryptography
WebViews
Storage
Deep links
Security controls
```

---

# 4. String Analysis

Search the application for interesting strings.

Examples:

```text
https://
api.
token
authorization
bearer
password
secret
key
admin
debug
test
staging
firebase
graphql
```

Command:

```bash
strings classes.dex
```

or search directly inside JADX.

### Purpose

Identify potentially interesting functionality and locations for deeper analysis.

---

# 5. Cross-Reference Analysis

When you find an interesting class, method, or string, follow its references.

Example:

```text
String
 ↓
Cross Reference
 ↓
Method
 ↓
Calling Method
 ↓
API / Security Logic
```

Useful for understanding:

* Authentication flows
* Token generation
* Encryption/decryption
* API calls
* Deep-link handling
* Security checks

---

# 6. Smali Analysis

Decode the APK:

```bash
apktool d app.apk -o app
```

Analyze:

```text
smali/
smali_classes2/
smali_classes3/
```

Useful for understanding the exact DEX instructions when decompiled Java/Kotlin code is unclear.

---

# 7. Smali Modification

For authorized testing environments, modify Smali and rebuild the APK.

```bash
apktool b app -o modified.apk
```

Possible testing purposes:

```text
Validate client-side security controls
Understand application behavior
Test whether a client-side restriction is security-relevant
Create controlled test variants
```

The modification itself is **not a vulnerability**.

---

# 8. APK Patching

A patched test APK can be used to determine whether security decisions are enforced only on the client.

Example methodology:

```text
Original APK
     ↓
Identify client-side control
     ↓
Create controlled test modification
     ↓
Rebuild APK
     ↓
Install in test environment
     ↓
Repeat action
     ↓
Observe application/server behavior
```

The important question is:

```text
Does the backend independently enforce the security decision?
```

---

# 9. Runtime Instrumentation

### Frida

Use Frida to observe or modify runtime behavior in an authorized test environment.

Useful for:

```text
Method tracing
Function hooking
Parameter inspection
Return-value observation
Cryptographic analysis
Root detection analysis
SSL pinning analysis
Native function analysis
```

Example:

```bash
frida-ps -U
```

---

# 10. Method Hooking

Conceptual workflow:

```text
Application
     ↓
Target Method
     ↓
Frida Hook
     ↓
Observe Arguments
     ↓
Observe Return Value
     ↓
Understand Runtime Behavior
```

Useful for validating assumptions made during static analysis.

---

# 11. Native Function Hooking

For applications containing:

```text
lib/*.so
```

you can investigate native functions using:

```text
Frida
Ghidra
IDA
```

Workflow:

```text
.so
 ↓
Ghidra
 ↓
Identify function
 ↓
Understand parameters
 ↓
Find cross-references
 ↓
Runtime hook
 ↓
Validate behavior
```

---

# 12. Native Library Analysis

Extract:

```text
lib/arm64-v8a/*.so
```

Determine architecture:

```bash
file libnative.so
```

Inspect ELF headers:

```bash
readelf -h libnative.so
```

Analyze using:

```text
Ghidra
IDA
Binary Ninja
radare2
Cutter
```

---

# 13. JNI Analysis

Search for:

```text
System.loadLibrary()
JNIEXPORT
Java_*
RegisterNatives
```

Understand:

```text
Java/Kotlin
     ↓
JNI
     ↓
C/C++
     ↓
Native Library
```

This is particularly important when security-sensitive logic is moved into native code.

---

# 14. SSL/TLS Inspection

Static analysis:

```text
CertificatePinner
TrustManager
X509TrustManager
HostnameVerifier
SSLContext
Network Security Config
```

Dynamic analysis:

```text
Frida
Objection
Burp Suite
```

Workflow:

```text
Identify TLS implementation
        ↓
Determine whether pinning exists
        ↓
Use authorized runtime instrumentation
        ↓
Route traffic through test proxy
        ↓
Inspect API behavior
```

---

# 15. API Reverse Engineering

Combine:

```text
JADX
    +
Burp Suite
    +
Frida
```

Workflow:

```text
Static Analysis
      ↓
Identify API endpoint
      ↓
Capture Request
      ↓
Understand Parameters
      ↓
Identify Authentication
      ↓
Identify Authorization
      ↓
Manipulate Request
      ↓
Validate Server Behavior
```

---

# 16. Deep-Link Analysis

Identify:

```text
URI schemes
App Links
Intent Filters
Exported Activities
```

Test:

```text
Parameter manipulation
Authentication requirements
Authorization requirements
Sensitive actions
Intent injection
Unexpected navigation
```

---

# 17. WebView Analysis

Search for:

```text
WebView
loadUrl()
addJavascriptInterface()
setJavaScriptEnabled()
setAllowFileAccess()
```

Then determine:

```text
What URLs can be loaded?
Can untrusted content reach the WebView?
Are JavaScript interfaces exposed?
Can sensitive native functionality be invoked?
```

---

# 18. Local Storage Analysis

Identify:

```text
SharedPreferences
SQLite
Room
Files
Cache
Databases
Keystore
```

Then validate at runtime.

```text
Static Analysis
      ↓
Identify Storage
      ↓
Install / Run App
      ↓
Perform Sensitive Action
      ↓
Inspect Device Storage
      ↓
Determine Whether Sensitive Data Is Exposed
```

---

# 19. Cryptographic Analysis

Static search:

```text
Cipher
MessageDigest
Mac
KeyStore
SecretKey
RSA
AES
DES
MD5
SHA
Base64
```

Then determine:

```text
Algorithm
Mode
Padding
Key generation
Key storage
IV / nonce handling
Randomness
Purpose
```

Do not classify something as a vulnerability merely because it uses a particular cryptographic API. Assess the **implementation and security context**.

---

# 20. Root Detection Analysis

Search for:

```text
su
/system/bin/su
/system/xbin/su
Magisk
root
SafetyNet
Play Integrity
```

Use runtime instrumentation to understand:

```text
Where detection occurs
What functions are called
What happens when detection succeeds
Whether it affects security or merely application functionality
```

---

# 21. Anti-Debugging Analysis

Look for:

```text
ptrace
TracerPid
debugger detection
emulator detection
Frida detection
process inspection
```

Use static and dynamic analysis to determine how the application responds.

---

# 22. Binary Patching

Use:

```text
Ghidra
IDA
radare2
Cutter
Hex editors
Apktool
```

Potential testing purposes:

```text
Validate client-side enforcement
Study control flow
Understand security checks
Create controlled research variants
```

Workflow:

```text
Identify Security Check
        ↓
Understand Control Flow
        ↓
Patch Test Copy
        ↓
Rebuild / Save
        ↓
Execute
        ↓
Observe
        ↓
Determine Security Impact
```

---

# 🛡️ Part 2 — Security Findings

The following are **potential findings** that can be identified or validated using reverse engineering.

---

## 🔴 Authentication & Session Management

### Potential Findings

```text
Weak Authentication Logic
Hardcoded Credentials
Hardcoded Authentication Tokens
Insecure Token Storage
Long-Lived Tokens
Improper Logout
Session Reuse
Weak Token Validation
Client-Side Authentication Enforcement
```

### Validation

Do not rely only on the source code.

Validate the behavior through:

```text
Application
   ↓
Burp Suite
   ↓
API
   ↓
Server Response
```

---

# 🔴 Authorization

Potential findings:

```text
Broken Access Control
IDOR / BOLA
Client-Side Authorization
Privilege Escalation
Unauthorized Functionality
```

Example:

```text
Client:
if (isAdmin)
    showAdminFunction();
```

This is not automatically a vulnerability.

Test the corresponding API with an authorized lower-privileged account.

---

# 🟠 Sensitive Data Exposure

Potential findings:

```text
Passwords stored insecurely
Authentication tokens stored insecurely
PII stored insecurely
Sensitive API responses cached locally
Sensitive information in logs
Secrets inside application files
```

Validate whether the information is actually sensitive and exploitable.

---

# 🟠 Hardcoded Secrets

Potential findings:

```text
API keys
Private credentials
Service credentials
Encryption keys
Access tokens
Third-party secrets
```

Validation should establish:

```text
Is the value secret?
Is it active?
Can it be used outside the application?
What privileges does it provide?
```

A public identifier or non-sensitive configuration value is not automatically a vulnerability.

---

# 🟠 Insecure Data Storage

Potential findings:

```text
Sensitive SharedPreferences
Unencrypted SQLite data
Sensitive files
Authentication tokens in plaintext
Sensitive logs
Insecure backups
```

Useful locations:

```text
/data/data/<package>/
```

---

# 🟠 Cryptographic Weaknesses

Potential findings:

```text
Weak algorithms
Hardcoded cryptographic keys
Predictable keys
Insecure IV/nonce handling
Improper encryption modes
Weak hashing for security-sensitive purposes
Custom cryptography
Improper key storage
```

---

# 🟠 Network Security

Potential findings:

```text
Cleartext HTTP
Improper TLS validation
Weak hostname verification
Improper certificate validation
Sensitive data transmitted insecurely
```

SSL pinning bypass itself should generally be treated as a **testing technique**, not automatically as a vulnerability.

---

# 🟠 Android Component Security

Potential findings:

```text
Exported sensitive Activity
Exported Service
Exported Broadcast Receiver
Exported Content Provider
Improper permission enforcement
Intent injection
Sensitive functionality accessible through IPC
```

---

# 🟠 Deep-Link Security

Potential findings:

```text
Authentication bypass through deep links
Authorization bypass
Sensitive action through crafted URI
Intent injection
Untrusted URL handling
Account takeover through malicious redirect
```

---

# 🟠 WebView Security

Potential findings:

```text
JavaScript injection
Unsafe JavaScript interface
Untrusted URL loading
Unsafe file access
Improper origin validation
Sensitive native functionality exposed to WebView
```

---

# 🟠 Root / Security Control Weaknesses

Potential findings may include:

```text
Security-sensitive functionality relying exclusively
on client-side root detection
```

However:

```text
Root Detection Bypass
```

by itself is generally a **testing result**, not necessarily a reportable vulnerability.

Determine what security boundary the bypass affects.

---

# 🟠 Client-Side Security Controls

Potential findings:

```text
Client-only authorization
Client-only privilege checks
Client-only transaction validation
Client-only security restrictions
Security decisions trusted from untrusted client state
```

The key validation step is:

```text
Can the backend be convinced to perform the
restricted operation without the legitimate privilege?
```

---

# 🟠 Native Code Security

Potential findings:

```text
Insecure native cryptography
Sensitive data in native memory
Unsafe JNI interfaces
Memory corruption
Command injection through native code
Improper input validation
```

Native reverse engineering is particularly useful for discovering logic that is not visible in the Java/Kotlin layer.

---

# 🟡 Debug / Development Artifacts

Potential findings:

```text
Debug logging
Test credentials
Debug endpoints
Staging endpoints
Developer menus
Test functionality
Verbose error messages
```

Again, validate the security impact before reporting.

---

# 📊 Technique → Finding Mapping

| Testing Technique       | Potential Finding                     |
| ----------------------- | ------------------------------------- |
| JADX analysis           | Hardcoded secrets                     |
| Manifest analysis       | Exported component                    |
| Smali analysis          | Client-side security control          |
| APK patching            | Client-side enforcement weakness      |
| Frida hooking           | Runtime security weakness             |
| Burp interception       | API authorization issue               |
| Ghidra analysis         | Native security weakness              |
| JNI analysis            | Native authentication/crypto logic    |
| Storage analysis        | Insecure data storage                 |
| String analysis         | Exposed endpoints/secrets             |
| Deep-link analysis      | Deep-link authorization issue         |
| WebView analysis        | WebView security issue                |
| Crypto analysis         | Cryptographic weakness                |
| SSL analysis            | TLS validation issue                  |
| Root detection analysis | Client-side security control          |
| Binary patching         | Validation of client-side enforcement |

---

# 🧠 Finding Validation Rule

Use this methodology for every suspected issue:

```text
          Suspected Issue
                 │
                 ▼
          Static Evidence
                 │
                 ▼
          Dynamic Testing
                 │
                 ▼
        Server-Side Validation
                 │
                 ▼
           Security Impact
                 │
                 ▼
        Exploitability Check
                 │
                 ▼
          Report Finding
```

A good mobile pentester should avoid reporting:

```text
"I found a string."

"I found an API endpoint."

"I bypassed SSL pinning."

"I bypassed root detection."

"I modified the APK."

```

as vulnerabilities without establishing the **actual security impact**.

Instead, establish:

```text
What is exposed?
       +
Can an attacker exploit it?
       +
What privilege is required?
       +
What security boundary is affected?
       +
What is the impact?
```

---

# 📋 Mobile Reverse Engineering Checklist

```text
[ ] APK identification
[ ] Package information
[ ] Architecture / ABI
[ ] Manifest analysis
[ ] Permissions
[ ] Exported components
[ ] Intent filters
[ ] Deep links
[ ] App Links
[ ] DEX analysis
[ ] JADX analysis
[ ] Smali analysis
[ ] String analysis
[ ] Cross-reference analysis
[ ] API endpoint discovery
[ ] Authentication logic
[ ] Authorization logic
[ ] Token handling
[ ] Hardcoded secrets
[ ] Local storage
[ ] SQLite / Room
[ ] SharedPreferences
[ ] WebView
[ ] Cryptography
[ ] TLS implementation
[ ] SSL pinning
[ ] Root detection
[ ] Anti-debugging
[ ] Native libraries
[ ] JNI
[ ] ARM64 analysis
[ ] Runtime instrumentation
[ ] Frida analysis
[ ] Burp traffic analysis
[ ] Controlled APK patching
[ ] Server-side validation
[ ] Finding confirmation
[ ] Evidence collection
[ ] Reporting
```

---

# 🎯 Final Methodology

A professional mobile reverse-engineering assessment should follow:

```text
RECON
  ↓
STATIC ANALYSIS
  ↓
REVERSE ENGINEERING
  ↓
HYPOTHESIS
  ↓
DYNAMIC ANALYSIS
  ↓
TRAFFIC ANALYSIS
  ↓
CONTROLLED MODIFICATION
  ↓
SERVER-SIDE VALIDATION
  ↓
IMPACT ASSESSMENT
  ↓
SECURITY FINDING
  ↓
REPORT
```

**Techniques tell you how to investigate. Findings tell you what security weakness you actually demonstrated.**
