# 🔎 JADX

A complete beginner-to-advanced guide to using **JADX for Android application reverse engineering and mobile application security testing**.

JADX is an open-source Android decompiler that converts **DEX bytecode into Java-like source code**, making Android application logic easier to understand during authorized security assessments.

This guide covers:

* JADX installation
* APK and DEX analysis
* Java/Kotlin decompilation
* AndroidManifest analysis
* Package, class, method, and field navigation
* Search and string analysis
* API endpoint discovery
* Authentication and authorization analysis
* JWT and token analysis
* Hardcoded secrets
* Local storage
* SharedPreferences
* SQLite
* Cryptography
* WebView analysis
* Deep-link analysis
* Exported components
* Intent analysis
* Network security
* SSL/TLS and certificate pinning
* Root detection
* Anti-debugging
* Obfuscation
* Smali relationship
* JADX + apktool
* JADX + Ghidra
* JADX + Frida
* JADX + Burp Suite
* Decompiler limitations
* Testing technique vs security finding
* Complete beginner practice lab
* Intermediate and advanced practice
* JADX cheat sheet
* Learning roadmap
* Final checklist

> **Scope:** Use JADX only against applications you own, applications you are authorized to assess, or intentionally vulnerable applications, CTFs, crackmes, and security-training targets.

---

# 📚 Table of Contents

* [1. What is JADX?](#1-what-is-jadx)
* [2. Why JADX for Mobile Pentesting?](#2-why-jadx-for-mobile-pentesting)
* [3. JADX Architecture](#3-jadx-architecture)
* [4. Prerequisites](#4-prerequisites)
* [5. Installation](#5-installation)
* [6. JADX Components](#6-jadx-components)
* [7. Starting JADX](#7-starting-jadx)
* [8. Opening an APK](#8-opening-an-apk)
* [9. JADX Interface](#9-jadx-interface)
* [10. APK Structure](#10-apk-structure)
* [11. Package Analysis](#11-package-analysis)
* [12. Classes](#12-classes)
* [13. Methods](#13-methods)
* [14. Fields](#14-fields)
* [15. Code Navigation](#15-code-navigation)
* [16. Search](#16-search)
* [17. Strings](#17-strings)
* [18. API Endpoint Discovery](#18-api-endpoint-discovery)
* [19. Authentication Analysis](#19-authentication-analysis)
* [20. Authorization Analysis](#20-authorization-analysis)
* [21. JWT and Token Analysis](#21-jwt-and-token-analysis)
* [22. Hardcoded Secrets](#22-hardcoded-secrets)
* [23. Local Storage](#23-local-storage)
* [24. SharedPreferences](#24-sharedpreferences)
* [25. SQLite](#25-sqlite)
* [26. Cryptography](#26-cryptography)
* [27. WebView Analysis](#27-webview-analysis)
* [28. Deep Link Analysis](#28-deep-link-analysis)
* [29. Exported Components](#29-exported-components)
* [30. Intent Analysis](#30-intent-analysis)
* [31. Network Security](#31-network-security)
* [32. SSL/TLS and Certificate Pinning](#32-ssltls-and-certificate-pinning)
* [33. Root Detection](#33-root-detection)
* [34. Anti-Debugging](#34-anti-debugging)
* [35. Obfuscation](#35-obfuscation)
* [36. Smali Relationship](#36-smali-relationship)
* [37. JADX + apktool](#37-jadx--apktool)
* [38. JADX + Ghidra](#38-jadx--ghidra)
* [39. JADX + Frida](#39-jadx--frida)
* [40. JADX + Burp Suite](#40-jadx--burp-suite)
* [41. Understanding Decompiler Limitations](#41-understanding-decompiler-limitations)
* [42. JADX Analysis Workflow](#42-jadx-analysis-workflow)
* [43. Testing Technique vs Security Finding](#43-testing-technique-vs-security-finding)
* [44. JADX Technique → Finding Mapping](#44-jadx-technique--finding-mapping)
* [45. Complete Beginner Practice Lab](#45-complete-beginner-practice-lab)
* [46. Intermediate Practice](#46-intermediate-practice)
* [47. Advanced Practice](#47-advanced-practice)
* [48. JADX Cheat Sheet](#48-jadx-cheat-sheet)
* [49. Learning Roadmap](#49-learning-roadmap)
* [50. Final Checklist](#50-final-checklist)
* [51. Responsible Use](#51-responsible-use)

---

# 1. What is JADX?

JADX is an open-source Android decompiler.

Its primary purpose is to convert:

```text
Android APK
     ↓
DEX bytecode
     ↓
Java-like source code
```

For example:

```text
classes.dex
classes2.dex
classes3.dex
```

can be represented by JADX as Java-like classes:

```java
public class LoginActivity {
    ...
}
```

This makes application logic significantly easier to inspect.

---

# 2. Why JADX for Mobile Pentesting?

Android applications contain a large amount of security-relevant logic.

JADX can help identify:

```text
API endpoints
Authentication logic
Authorization logic
Token handling
Hardcoded secrets
Cryptographic operations
Local storage
WebViews
Deep links
Intent handling
Security controls
Debug functionality
Root detection
Certificate pinning
```

The important concept is:

```text
JADX
  ↓
Understand application
  ↓
Identify security-sensitive logic
  ↓
Build testing hypothesis
  ↓
Validate dynamically
```

JADX itself does **not** prove that a vulnerability exists.

---

# 3. JADX Architecture

Conceptually:

```text
                 APK
                  │
                  ▼
              classes.dex
                  │
                  ▼
                 JADX
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
 Java-like code        Resources
        │
        ▼
 Security Analysis
```

JADX reconstructs Java-like code from compiled DEX bytecode.

It does not recover the original source code perfectly.

---

# 4. Prerequisites

## Android Knowledge

Understand the basics of:

```text
Activities
Services
Broadcast Receivers
Content Providers
Intents
Permissions
Manifest
APK
DEX
```

## Programming

Basic knowledge of:

```text
Java
Kotlin
Classes
Methods
Variables
Conditions
Loops
Object-oriented programming
```

Advanced Java development is **not required** to start.

## Security

Know the basics of:

```text
HTTP
HTTPS
REST APIs
Authentication
Authorization
JWT
Sessions
TLS
```

## Recommended Tools

```text
JADX
ADB
apktool
Burp Suite
Android Studio
Ghidra
Frida
```

---

# 5. Installation

Official JADX repository:

https://github.com/skylot/jadx

Download the appropriate release for your operating system.

JADX generally provides:

```text
jadx
jadx-gui
```

---

## Verify Installation

```bash
jadx --version
```

Start the GUI:

```bash
jadx-gui
```

---

# 6. JADX Components

JADX has two primary interfaces.

## JADX GUI

```bash
jadx-gui
```

Best for:

```text
Interactive analysis
Code navigation
Searching
XREF analysis
Manual investigation
```

## JADX CLI

```bash
jadx
```

Useful for exporting decompiled code.

Example:

```bash
jadx -d output app.apk
```

---

# 7. Starting JADX

GUI:

```bash
jadx-gui
```

You can then open the target APK through:

```text
File
 ↓
Open
```

Or directly:

```bash
jadx-gui application.apk
```

---

# 8. Opening an APK

Example:

```bash
jadx-gui application.apk
```

JADX processes important application components such as:

```text
AndroidManifest.xml
classes.dex
classes2.dex
resources
```

After processing, the project becomes navigable through the JADX interface.

---

# 9. JADX Interface

The interface generally contains:

```text
┌────────────────────────────────────────────┐
│ Menu / Toolbar                             │
├───────────────┬────────────────────────────┤
│ Project Tree  │ Decompiled Code             │
│               │                            │
│ Packages      │ public class MainActivity  │
│ Classes       │ {                          │
│ Resources     │     ...                    │
│               │ }                          │
├───────────────┴────────────────────────────┤
│ Search / Navigation / Information          │
└────────────────────────────────────────────┘
```

Important areas:

```text
Packages
Classes
Methods
Fields
Resources
AndroidManifest
Search
Code
```

---

# 10. APK Structure

An APK commonly contains:

```text
application.apk
├── AndroidManifest.xml
├── classes.dex
├── classes2.dex
├── resources.arsc
├── res/
├── assets/
├── lib/
├── META-INF/
└── unknown/
```

For static security analysis, pay particular attention to:

```text
AndroidManifest.xml
classes.dex
classes2.dex
resources
assets
lib/
```

---

# 11. Package Analysis

Start with the package structure.

Example:

```text
com.example.app
├── MainActivity
├── LoginActivity
├── NetworkManager
├── ApiClient
├── DatabaseHelper
└── SecurityManager
```

Look for packages related to:

```text
auth
authentication
security
crypto
network
api
database
storage
payment
login
admin
debug
```

Do not attempt to read the entire application.

Start with functionality relevant to the assessment.

---

# 12. Classes

A class is a major unit of application functionality.

Example:

```java
public class LoginActivity {
}
```

Interesting classes may include:

```text
LoginActivity
RegisterActivity
ApiClient
NetworkManager
AuthManager
TokenManager
DatabaseHelper
CryptoManager
WebViewActivity
DeepLinkActivity
SecurityManager
```

---

# 13. Methods

Methods contain application behavior.

Example:

```java
public void login(String username, String password) {
    ...
}
```

Search for security-relevant methods such as:

```text
login()
logout()
authenticate()
authorize()
validateToken()
refreshToken()
encrypt()
decrypt()
saveToken()
getToken()
loadUrl()
verifyCertificate()
```

When you find a relevant method, trace its callers and callees.

---

# 14. Fields

Fields can reveal:

```text
API URLs
Configuration
Tokens
Keys
Flags
Identifiers
```

Example:

```java
private String baseUrl = "https://api.example.test";
```

Or:

```java
private static final String API_KEY = "...";
```

Do not automatically classify every constant as a vulnerability.

Determine:

```text
What is it?
Is it sensitive?
Is it valid?
What permissions does it provide?
Can it be abused?
```

---

# 15. Code Navigation

One of JADX's most useful capabilities is navigating references.

A useful investigation pattern is:

```text
Interesting Method
       ↓
Caller
       ↓
Callee
       ↓
Field
       ↓
String
       ↓
Runtime Behavior
```

When investigating a method, ask:

```text
Who calls this?

What does it call?

What parameters does it receive?

What does it return?

Where does the result go?

Does the result influence a security decision?
```

---

# 16. Search

Search is one of the most important JADX features.

Useful search terms:

```text
login
logout
password
token
jwt
authorization
bearer
admin
role
permission
secret
api
https
http
WebView
loadUrl
SharedPreferences
SQLite
Cipher
AES
RSA
SSL
TLS
certificate
root
debug
```

Search can quickly reduce a large application into a manageable set of interesting classes and methods.

---

# 17. Strings

Search for security-sensitive-looking strings.

Examples:

```text
api_key
password
token
secret
private_key
authorization
bearer
```

API-related strings:

```text
https://
http://
/api/
/v1/
/v2/
graphql
oauth
login
```

Security-related strings:

```text
root
magisk
frida
debugger
certificate
pinning
```

Important:

```text
String found
    ≠
Vulnerability
```

Always trace how the value is used.

---

# 18. API Endpoint Discovery

Search:

```text
https://
http://
/api/
graphql
retrofit
okhttp
baseUrl
endpoint
```

Example:

```java
private static final String BASE_URL =
    "https://api.example.test/";
```

Then investigate:

```text
What API uses this?

Which HTTP method is used?

What authentication is required?

What data is sent?

How is the response processed?
```

---

# 19. Authentication Analysis

Search:

```text
login
authenticate
password
credential
token
session
refresh
accessToken
```

Example:

```java
if (response.isSuccessful()) {
    saveToken(response.token);
}
```

Trace:

```text
Login
 ↓
Request
 ↓
Response
 ↓
Token
 ↓
Storage
 ↓
Subsequent requests
```

Questions:

```text
Where is the token stored?

How long is it valid?

Can it be reused?

What happens during logout?

Is authentication enforced server-side?
```

---

# 20. Authorization Analysis

Search:

```text
admin
isAdmin
role
permission
authorize
privilege
access
```

Example:

```java
if (user.isAdmin()) {
    showAdminPanel();
}
```

This does **not** automatically prove an authorization vulnerability.

Determine:

```text
Is this only UI logic?

Does the backend enforce authorization?

Can a normal user access the privileged server operation?
```

Security-sensitive authorization decisions should be enforced server-side.

---

# 21. JWT and Token Analysis

Search:

```text
JWT
Bearer
Authorization
accessToken
refreshToken
idToken
token
exp
iat
sub
```

Typical flow:

```text
Login
 ↓
JWT
 ↓
Store token
 ↓
Authorization header
 ↓
API
```

Example:

```http
Authorization: Bearer <token>
```

JADX can help identify:

```text
Token creation
Token parsing
Token storage
Token transmission
Token refresh
Logout handling
```

---

# 22. Hardcoded Secrets

Search:

```text
apiKey
api_key
secret
clientSecret
password
privateKey
accessToken
```

Example:

```java
String secret = "example-secret";
```

Investigate:

```text
Is it actually secret?

Is it production?

Is it privileged?

Can it authenticate?

Can it access sensitive resources?

Can it be reused externally?
```

Only after validation should it be classified as a security finding.

---

# 23. Local Storage

Look for:

```text
SharedPreferences
SQLite
Room
Files
Internal Storage
External Storage
Cache
```

Search:

```text
SharedPreferences
getSharedPreferences
putString
getString
SQLiteDatabase
RoomDatabase
```

Potentially sensitive data includes:

```text
Access tokens
Refresh tokens
PII
Credentials
Session identifiers
Encryption keys
```

Assess both the sensitivity of the data and the protection mechanism.

---

# 24. SharedPreferences

Common code:

```java
SharedPreferences prefs =
    getSharedPreferences("user_data", MODE_PRIVATE);
```

Writing:

```java
prefs.edit()
    .putString("token", token)
    .apply();
```

Reading:

```java
String token =
    prefs.getString("token", null);
```

Trace:

```text
Token
 ↓
SharedPreferences
 ↓
Filesystem
```

Questions:

```text
Is sensitive information stored?

Is it encrypted?

How is the encryption key protected?

Does logout remove it?

Can another application access it under the platform's security model?
```

Do not report SharedPreferences usage by itself.

---

# 25. SQLite

Search:

```text
SQLiteDatabase
SQLiteOpenHelper
RoomDatabase
@Database
@Dao
```

Look for tables containing:

```text
tokens
users
passwords
sessions
credentials
payments
PII
```

Then determine:

```text
What data is stored?
How sensitive is it?
How is it protected?
Is unnecessary data retained?
Does logout/account deletion remove it where appropriate?
```

---

# 26. Cryptography

Search:

```text
Cipher
SecretKey
SecretKeySpec
MessageDigest
Mac
KeyStore
AES
RSA
SHA
MD5
Hmac
```

Questions:

```text
Which algorithm?
Which mode?
Which padding?
Where is the key?
How is the IV generated?
Is randomness secure?
Is the key hardcoded?
What data is being protected?
```

Example:

```java
Cipher.getInstance("AES/ECB/PKCS5Padding");
```

The use of a particular algorithm or mode should be assessed in context.

---

# 27. WebView Analysis

Search:

```text
WebView
loadUrl
evaluateJavascript
addJavascriptInterface
setJavaScriptEnabled
setWebContentsDebuggingEnabled
```

Example:

```java
WebView webView = findViewById(R.id.webview);
webView.loadUrl(url);
```

Investigate:

```text
Where does the URL come from?

Can it be externally controlled?

Is JavaScript enabled?

Are JavaScript interfaces exposed?

Can sensitive data enter the WebView?

Are untrusted URLs loaded?
```

---

# 28. Deep Link Analysis

Search:

```text
Intent
Uri
getData
getScheme
getHost
getQueryParameter
```

Also inspect:

```text
AndroidManifest.xml
intent-filter
scheme
host
path
```

Example:

```java
Uri data = getIntent().getData();
```

Trace:

```text
External Intent
      ↓
URI
      ↓
Application
      ↓
Parser
      ↓
Sensitive Action
```

Potential issues can include:

```text
Unsafe deep-link handling
Authentication bypass
Authorization weaknesses
Sensitive action exposure
Untrusted input processing
```

---

# 29. Exported Components

Inspect:

```text
AndroidManifest.xml
```

Look for:

```xml
android:exported="true"
```

Important components:

```text
Activity
Service
BroadcastReceiver
ContentProvider
```

An exported component is **not automatically vulnerable**.

Ask:

```text
Can it be externally invoked?

What does it expose?

Does it require a permission?

Does it perform sensitive actions?

Does it trust attacker-controlled input?

Is authorization enforced?
```

---

# 30. Intent Analysis

Search:

```text
Intent
putExtra
getExtra
getStringExtra
getParcelableExtra
startActivity
startService
sendBroadcast
```

Trace:

```text
External Input
      ↓
Intent
      ↓
Extra / URI
      ↓
Application Logic
      ↓
Sensitive Operation
```

This can help identify:

```text
Intent injection
Sensitive component exposure
Unsafe input handling
Authorization weaknesses
```

---

# 31. Network Security

Search:

```text
OkHttp
Retrofit
HttpURLConnection
URLConnection
WebView
baseUrl
Request
Response
Interceptor
```

Investigate:

```text
Endpoints
HTTP methods
Headers
Authentication
Tokens
TLS
Certificate validation
Error handling
```

Correlate the static implementation with actual runtime traffic whenever possible.

---

# 32. SSL/TLS and Certificate Pinning

Search:

```text
CertificatePinner
TrustManager
HostnameVerifier
X509TrustManager
SSLContext
certificate
pin
pinning
```

Example:

```java
CertificatePinner certificatePinner =
    new CertificatePinner.Builder()
        .add("example.com", "sha256/...")
        .build();
```

Determine:

```text
Is pinning implemented?

Which hosts?

Which certificates?

How is validation enforced?

Are there fallback trust paths?
```

Bypassing certificate pinning during an authorized assessment is a **testing technique**, not automatically a vulnerability.

---

# 33. Root Detection

Search:

```text
root
isRooted
su
magisk
busybox
/system/bin/su
```

Example:

```java
if (new File("/system/bin/su").exists()) {
    return true;
}
```

Trace:

```text
Root Check
 ↓
Return Value
 ↓
Caller
 ↓
Application Decision
```

Determine whether the control:

```text
Only prevents analysis
```

or:

```text
Provides a security-critical control
```

---

# 34. Anti-Debugging

Search:

```text
debug
debugger
isDebuggerConnected
TracerPid
ptrace
```

Example:

```java
Debug.isDebuggerConnected()
```

Investigate:

```text
What happens when detection returns true?

Does the application terminate?

Does it disable security-sensitive functionality?

Is it simply an anti-analysis mechanism?
```

---

# 35. Obfuscation

Production Android applications may use:

```text
R8
ProGuard
Name obfuscation
String obfuscation
Control-flow obfuscation
Reflection
Dynamic class loading
```

Instead of:

```java
authenticateUser()
```

you may see:

```java
a()
```

or:

```java
x.y.a()
```

Do not rely only on names.

Use:

```text
Strings
Call hierarchy
Method behavior
Constants
XREF
API usage
```

---

# 36. Smali Relationship

JADX and apktool complement each other.

```text
             APK
              │
       ┌──────┴──────┐
       ▼             ▼
     JADX          apktool
       │             │
       ▼             ▼
 Java-like         Smali
 code              code
       │             │
       └──────┬──────┘
              ▼
        Android Logic
```

Use JADX when you want:

```text
Readable Java-like code
Class navigation
Method analysis
XREF analysis
```

Use apktool/Smali when you need:

```text
Lower-level inspection
DEX instruction analysis
Controlled test modifications
```

---

# 37. JADX + apktool

Typical relationship:

```text
APK
 │
 ├── JADX
 │    └── Understand logic
 │
 └── apktool
      └── Inspect Smali
```

Commands:

```bash
jadx -d jadx-output app.apk
```

```bash
apktool d app.apk -o decoded
```

Use both when JADX's decompiled representation does not completely explain the behavior.

---

# 38. JADX + Ghidra

When JADX reveals:

```java
System.loadLibrary("native");
```

look for:

```text
libnative.so
```

Then:

```text
JADX
 ↓
Identify native method
 ↓
Find .so
 ↓
Ghidra
 ↓
Analyze native implementation
```

This is particularly useful for:

```text
JNI
Native authentication
Native crypto
Root detection
Anti-debugging
String comparison
Security-sensitive native functions
```

---

# 39. JADX + Frida

JADX tells you:

```text
Class
Method
Parameters
Return type
Call flow
```

Frida can help validate runtime behavior.

Example relationship:

```text
JADX
 ↓
Find interesting method
 ↓
Understand parameters
 ↓
Understand return value
 ↓
Frida
 ↓
Observe runtime behavior
```

Useful for investigating:

```text
Authentication
Root detection
Certificate validation
Crypto
Sensitive methods
Runtime security decisions
```

---

# 40. JADX + Burp Suite

JADX:

```text
Understand API implementation
```

Burp:

```text
Observe actual HTTP traffic
```

Combined:

```text
JADX
 ↓
ApiClient
 ↓
Endpoint
 ↓
HTTP request
 ↓
Burp
 ↓
Runtime validation
```

Correlate:

```text
URL
HTTP method
Headers
Authorization
Parameters
JSON body
Response handling
```

---

# 41. Understanding Decompiler Limitations

JADX does not recover the original source code perfectly.

Potential complications include:

```text
Obfuscation
Compiler transformations
Synthetic methods
Generics
Kotlin artifacts
Coroutines
Reflection
Optimization
Incorrect variable names
Missing type information
```

Therefore:

```text
JADX output
     ↓
Security hypothesis
     ↓
Smali validation when necessary
     ↓
Dynamic validation
```

For native code:

```text
JADX
   ↓
Ghidra
```

---

# 42. JADX Analysis Workflow

This is the **single canonical workflow** for JADX-based mobile application analysis.

```text
                         APK
                          │
                          ▼
                        JADX
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
      Manifest         Packages         Search
          │               │               │
          └───────────────┼───────────────┘
                          ▼
                  Interesting Classes
                          │
                          ▼
                       Methods
                          │
                          ▼
                         XREF
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
       Network          Storage          Security
          │               │               │
          ▼               ▼               ▼
       API Flow        Data Flow       Security Logic
          │               │               │
          └───────────────┼───────────────┘
                          ▼
                  Security Hypothesis
                          │
                          ▼
                 Dynamic Validation
                          │
                          ▼
                     Confirm Impact
                          │
                          ▼
                       Report
```

## Step 1 — Identify the Application

Record:

```text
Package
Version
SDK
Permissions
DEX files
Native libraries
```

## Step 2 — Inspect the Manifest

Identify:

```text
Activities
Services
Receivers
Providers
Exported components
Intent filters
Deep links
Permissions
```

## Step 3 — Understand the Package Structure

Find:

```text
Authentication
Networking
Storage
Crypto
Security
Payment
WebView
Deep-link
```

## Step 4 — Search

Start with high-value terms:

```text
login
token
password
authorization
api
https
secret
SharedPreferences
SQLite
WebView
Intent
Cipher
certificate
root
debug
```

## Step 5 — Trace Interesting Code

Use:

```text
Class
 ↓
Method
 ↓
Caller
 ↓
Callee
 ↓
Field
 ↓
Data flow
```

## Step 6 — Build a Security Hypothesis

Example:

```text
Token
 ↓
Stored in SharedPreferences
 ↓
No apparent protection
 ↓
Token remains after logout
```

This is a **hypothesis**, not yet a confirmed finding.

## Step 7 — Validate

Use the appropriate tool:

```text
ADB
Burp Suite
Frida
apktool
Ghidra
```

depending on the hypothesis.

## Step 8 — Confirm Impact

Determine:

```text
Can it actually be exploited?

What privileges are required?

What security boundary is crossed?

What data or functionality is affected?
```

## Step 9 — Document

Record:

```text
Title
Severity
Affected component
Description
Evidence
Reproduction
Impact
Remediation
```

---

# 43. Testing Technique vs Security Finding

This distinction is extremely important.

## Testing Technique

A technique is a method used to investigate an application.

Examples:

```text
JADX
String search
XREF
Code navigation
Smali analysis
Frida
Burp
APK modification
Root bypass
SSL pinning bypass
```

## Security Finding

A finding is an actual security weakness.

Examples:

```text
Hardcoded credential
Insecure storage
Broken authorization
Improper certificate validation
Sensitive exported component
Weak cryptography
Insecure deep link
Sensitive information exposure
```

### Example

Finding:

```text
CertificatePinner
```

is not automatically a vulnerability.

Bypassing certificate pinning:

```text
Testing technique
```

Improper TLS certificate validation:

```text
Potential security finding
```

Always validate the actual security impact.

---

# 44. JADX Technique → Finding Mapping

| JADX Technique             | Potential Finding                        |
| -------------------------- | ---------------------------------------- |
| String search              | Hardcoded secret                         |
| API discovery              | Sensitive endpoint exposure              |
| Authentication tracing     | Authentication weakness                  |
| Authorization tracing      | Access-control weakness                  |
| SharedPreferences analysis | Insecure storage                         |
| SQLite analysis            | Sensitive data exposure                  |
| Crypto analysis            | Weak cryptography                        |
| WebView analysis           | WebView security issue                   |
| Deep-link analysis         | Insecure deep link                       |
| Manifest analysis          | Exported component issue                 |
| Intent tracing             | Intent injection                         |
| TLS analysis               | Certificate validation issue             |
| Root analysis              | Improper client-side security dependency |
| Token analysis             | Token handling weakness                  |

> **Potential finding ≠ confirmed finding.**

Always validate:

```text
Reachability
Exploitability
Security impact
Server-side behavior
```

---

# 45. Complete Beginner Practice Lab

## 🎯 Objective

Learn JADX from the beginning using an intentionally vulnerable Android application.

Recommended training targets:

```text
DIVA
InjuredAndroid
AndroidGoat
InsecureBankv2
MobileHackingLab Android applications
```

Use only an authorized training target.

---

## Lab Structure

Create:

```text
jadx-lab/
├── vulnerable.apk
├── notes.md
└── evidence/
```

---

## Step 1 — Open the APK

```bash
jadx-gui vulnerable.apk
```

Wait for JADX to finish processing.

---

## Step 2 — Identify the Package

Open:

```text
AndroidManifest.xml
```

Record:

```text
Package name
Version
Minimum SDK
Target SDK
Permissions
```

Example:

```markdown
# JADX Lab

## Application

Package:
Version:
Min SDK:
Target SDK:

## Observations

-
-
-
```

---

## Step 3 — Explore Packages

Look for:

```text
MainActivity
LoginActivity
DatabaseHelper
ApiClient
SecurityManager
CryptoManager
WebViewActivity
```

Choose one feature rather than attempting to understand the entire application.

---

## Step 4 — Search Authentication

Search:

```text
login
password
authenticate
token
session
```

Find the relevant class and read the complete method.

Ask:

```text
Where is the username read?

Where is the password read?

Where is the request sent?

What happens after authentication?

Where is the token stored?
```

---

## Step 5 — Find API Endpoints

Search:

```text
https://
http://
/api/
baseUrl
Retrofit
OkHttp
```

Record interesting endpoints:

```markdown
## API Observations

- Base URL:
- Login endpoint:
- User endpoint:
- Token endpoint:
```

---

## Step 6 — Trace the API

If you find:

```java
api.login(username, password);
```

navigate into:

```text
login()
```

Then trace:

```text
login()
 ↓
HTTP request
 ↓
Response
 ↓
Token
 ↓
Storage
```

---

## Step 7 — Investigate Token Storage

Search:

```text
SharedPreferences
putString
getString
accessToken
refreshToken
```

If you find:

```java
prefs.edit()
    .putString("token", token)
    .apply();
```

trace:

```text
Where is the token created?
Where is it stored?
Where is it retrieved?
Where is it sent?
Does logout remove it?
```

Do not report merely because SharedPreferences is used.

---

## Step 8 — Investigate Cryptography

Search:

```text
Cipher
AES
RSA
MD5
SHA
SecretKey
```

Record:

```text
Algorithm
Mode
Padding
Key source
IV source
Purpose
```

---

## Step 9 — Investigate WebView

Search:

```text
WebView
loadUrl
addJavascriptInterface
setJavaScriptEnabled
```

Trace:

```text
URL source
 ↓
WebView
 ↓
JavaScript
 ↓
Sensitive functionality
```

---

## Step 10 — Investigate Deep Links

Search:

```text
getIntent
getData
Uri
getQueryParameter
```

Then inspect the Manifest for:

```text
intent-filter
scheme
host
path
```

Determine what happens when external input reaches the activity.

---

## Step 11 — Select a Security-Sensitive Function

Example:

```text
validateToken()
```

Record:

```text
Class:
Method:
Arguments:
Return value:
Caller:
Callees:
```

---

## Step 12 — Trace XREF

Determine:

```text
Who calls this function?
```

Then follow:

```text
Function
 ↓
Caller
 ↓
Security decision
```

Determine whether the method affects:

```text
Authentication
Authorization
Sensitive data
Security configuration
```

---

## Step 13 — Validate Dynamically

Install the application:

```bash
adb install vulnerable.apk
```

Launch:

```bash
adb shell monkey -p <package-name> 1
```

For API-related hypotheses:

```text
JADX
 ↓
Find request
 ↓
Burp
 ↓
Capture request
 ↓
Validate behavior
```

For runtime code behavior:

```text
JADX
 ↓
Find method
 ↓
Frida
 ↓
Observe execution
```

---

## Step 14 — Decide Whether It Is a Finding

Use this decision process:

```text
Interesting Code
       ↓
Security Sensitive?
       │
       ▼
Reachable?
       │
       ▼
Attacker Controlled?
       │
       ▼
Can It Be Abused?
       │
       ▼
Real Security Impact?
       │
       ├── NO → Testing observation
       │
       └── YES → Security finding
```

---

## Step 15 — Document the Finding

Use:

```markdown
# Finding: <Title>

## Severity

<Severity>

## Description

<What is wrong?>

## Affected Component

<Class / Method / Activity>

## Technical Details

<Explain the relevant code>

## Steps to Reproduce

1.
2.
3.

## Evidence

<JADX screenshot / code location>
<Runtime evidence>

## Impact

<What can an attacker achieve?>

## Remediation

<Recommended fix>
```

---

# 46. Intermediate Practice

After completing the beginner lab, practice:

```text
Authentication
Authorization
JWT
Deep Links
WebView
SharedPreferences
SQLite
Cryptography
Certificate Pinning
Root Detection
Exported Components
```

For each topic:

```text
1. Find the relevant class
2. Find the method
3. Trace callers
4. Trace data
5. Understand the logic
6. Validate dynamically
7. Determine impact
8. Document
```

---

# 47. Advanced Practice

Learn to analyze:

```text
Reflection
Dynamic class loading
DexClassLoader
Runtime.exec
JNI
Native libraries
Kotlin coroutines
R8/ProGuard
Obfuscated code
Encrypted strings
Custom serialization
Custom crypto
Certificate validation
Anti-debugging
Root detection
```

Then combine:

```text
JADX
+
apktool
+
Ghidra
+
Frida
+
Burp
+
ADB
```

The purpose of combining these tools is not to repeat the same analysis, but to investigate different layers:

```text
JADX    → Java/Kotlin application logic
apktool → Smali/resources
Ghidra  → Native code
Frida   → Runtime behavior
Burp    → HTTP/API behavior
ADB     → Android device interaction
```

---

# 48. JADX Cheat Sheet

## Start GUI

```bash
jadx-gui app.apk
```

## Decompile

```bash
jadx -d output app.apk
```

## Common searches

```text
https://
http://
api
login
password
token
jwt
authorization
bearer
secret
key
SharedPreferences
SQLite
WebView
loadUrl
Intent
Uri
Cipher
AES
RSA
SSL
TLS
Certificate
root
debug
```

## Android classes worth investigating

```text
Activity
Service
BroadcastReceiver
ContentProvider
WebView
Intent
SharedPreferences
SQLiteDatabase
```

## Security classes

```text
AuthManager
TokenManager
CryptoManager
SecurityManager
NetworkManager
ApiClient
```

---

# 49. Learning Roadmap

## 🟢 Level 1 — Beginner

Learn:

```text
APK
DEX
Manifest
Packages
Classes
Methods
Fields
JADX GUI
Search
Navigation
```

Goal:

> Understand how to navigate an Android application's code.

---

## 🟡 Level 2 — Mobile Security

Learn:

```text
Authentication
Authorization
JWT
API endpoints
Storage
WebView
Deep links
Intents
Cryptography
TLS
```

Goal:

> Identify security-sensitive application logic.

---

## 🟠 Level 3 — Advanced Android

Learn:

```text
Smali
JNI
Reflection
Dynamic loading
Obfuscation
R8
Native libraries
```

Goal:

> Understand applications when normal Java/Kotlin analysis becomes insufficient.

---

## 🔴 Level 4 — Full Mobile Reverse Engineering

Combine:

```text
JADX
apktool
Ghidra
Frida
Burp Suite
ADB
```

Goal:

> Correlate static code, Smali, native code, runtime behavior, and network behavior.

---

# 50. Final Checklist

## APK

```text
[ ] Package name
[ ] Version
[ ] Min SDK
[ ] Target SDK
[ ] Permissions
[ ] DEX files
[ ] Native libraries
```

## Manifest

```text
[ ] Activities
[ ] Services
[ ] Receivers
[ ] Providers
[ ] Exported components
[ ] Intent filters
[ ] Deep links
[ ] App links
```

## Code

```text
[ ] Authentication
[ ] Authorization
[ ] Token handling
[ ] API endpoints
[ ] Hardcoded secrets
[ ] Local storage
[ ] Crypto
[ ] WebView
[ ] Intents
[ ] Deep links
[ ] TLS
[ ] Root detection
[ ] Anti-debugging
```

## Validation

```text
[ ] ADB
[ ] Burp
[ ] Frida
[ ] apktool
[ ] Ghidra
[ ] Runtime behavior
[ ] Server-side validation
```

## Reporting

```text
[ ] Evidence
[ ] Reproduction
[ ] Impact
[ ] Severity
[ ] CVSS
[ ] CWE
[ ] OWASP MASVS
[ ] Remediation
```

---

# 51. Responsible Use

JADX should be used responsibly.

Appropriate targets include:

```text
✓ Your own applications
✓ Authorized client applications
✓ Company-approved assessments
✓ Intentionally vulnerable applications
✓ CTF challenges
✓ Android crackmes
✓ Training labs
✓ Research applications
```

Do not reverse engineer or modify applications or services without appropriate authorization.

For professional assessments, maintain:

```text
Written authorization
Defined scope
Rules of engagement
Approved targets
Testing window
Evidence protection
Responsible disclosure
```

---

# 🎯 Final Takeaway

JADX is not simply an APK-to-Java converter.

For a mobile penetration tester, it is primarily a **code-understanding and security-analysis tool**.

The objective is:

```text
APK
 ↓
JADX
 ↓
Understand application logic
 ↓
Identify security-sensitive functionality
 ↓
Trace data and control flow
 ↓
Build security hypothesis
 ↓
Validate dynamically
 ↓
Confirm security impact
 ↓
Report
```

The goal is not:

> **"I found something suspicious in JADX."**

The goal is:

> **"I traced the application's code, understood how the relevant data and security decisions flow through it, validated the behavior at runtime, and demonstrated a real security impact."**

That is how JADX becomes a practical tool for **Android reverse engineering and mobile application penetration testing**.
