# 📱 Mobile Application Testing — Tools & Utilities

A practical collection of tools commonly used for **Android mobile application security testing**, covering static analysis, dynamic analysis, traffic interception, reverse engineering, runtime instrumentation, and APK analysis.

---

## 🗂️ Tool Categories

| Category                     | Tools                                           |
| ---------------------------- | ----------------------------------------------- |
| 🔍 Static Analysis           | JADX, MobSF, apktool, Ghidra                    |
| 🐞 Dynamic Analysis          | Frida, Objection, Drozer                        |
| 🌐 Traffic Analysis          | Burp Suite, mitmproxy                           |
| 📦 APK Analysis              | apktool, JADX, apksigner, zipalign              |
| 📱 Android Debugging         | ADB, Android Studio                             |
| 🔐 SSL Pinning               | Frida, Objection, SSL Kill Switch-style scripts |
| 🧬 Native Analysis           | Ghidra, IDA Free                                |
| 🧪 Automated Analysis        | MobSF                                           |
| 🖥️ Emulator / Device        | Android Emulator, Genymotion                    |
| 🔑 Cryptography Analysis     | CyberChef, OpenSSL                              |
| 📝 Reporting / Documentation | Markdown, CherryTree, Obsidian                  |

---

# 🔍 1. Static Analysis

Static analysis involves examining an application's APK without executing it.

### JADX

**Purpose:** Decompile Android APK/Dex files into readable Java-like source code.

Useful for:

* Reviewing application logic
* Finding hardcoded secrets
* Identifying API endpoints
* Understanding authentication mechanisms
* Reviewing cryptographic implementations
* Identifying exported components
* Searching for sensitive strings

```bash
jadx-gui application.apk
```

🔗 https://github.com/skylot/jadx

---

### MobSF — Mobile Security Framework

**Purpose:** Automated Android/iOS application security analysis.

Useful for:

* Manifest analysis
* Permissions
* Exported components
* Hardcoded secrets
* Insecure storage
* Cryptography checks
* Network security configuration
* Certificate analysis
* Code analysis

```bash
docker pull opensecurity/mobile-security-framework-mobsf
```

🔗 https://github.com/MobSF/Mobile-Security-Framework-MobSF

---

### Apktool

**Purpose:** Decode APK resources and smali code and rebuild APKs.

Useful for:

* Manifest analysis
* Smali analysis
* Modifying application resources
* Patching applications
* Rebuilding APKs
* Testing security controls

```bash
apktool d application.apk -o application
```

Rebuild:

```bash
apktool b application -o modified.apk
```

🔗 https://github.com/iBotPeaches/Apktool

---

# 🐞 2. Dynamic Analysis

Dynamic analysis involves testing the application while it is running.

### Frida

**Purpose:** Dynamic instrumentation toolkit.

Useful for:

* Method hooking
* Runtime manipulation
* SSL pinning testing
* Root detection bypass testing
* Cryptographic function analysis
* Native function hooking
* Runtime value modification

Example:

```bash
frida-ps -U
```

Attach to an application:

```bash
frida -U -n "Application Name"
```

🔗 https://github.com/frida/frida

---

### Objection

**Purpose:** Runtime mobile exploration powered by Frida.

Useful for:

* SSL pinning bypass
* Root detection testing
* Exploring application data
* Runtime inspection
* Keychain / Keystore analysis
* Filesystem exploration

```bash
objection -g com.example.app explore
```

🔗 https://github.com/sensepost/objection

---

### Drozer

**Purpose:** Android security assessment framework.

Useful for testing:

* Activities
* Services
* Broadcast Receivers
* Content Providers
* IPC
* Exported components

```bash
drozer console connect
```

🔗 https://github.com/WithSecureLabs/drozer

---

# 🌐 3. HTTP/HTTPS Traffic Analysis

### Burp Suite

**Purpose:** Intercepting and manipulating application network traffic.

Useful for:

* API testing
* Authentication testing
* Authorization testing
* Session management
* Parameter manipulation
* Request replay
* JWT analysis
* IDOR/BOLA testing
* Business logic testing

Common workflow:

```text
Android Application
        ↓
   Burp Proxy
        ↓
     API Server
```

🔗 https://portswigger.net/burp

---

### mitmproxy

Command-line / scriptable HTTP(S) interception proxy.

Useful for:

* API inspection
* Traffic modification
* Automation
* Python-based traffic processing

```bash
mitmproxy
```

🔗 https://github.com/mitmproxy/mitmproxy

---

# 📱 4. Android Debugging

### Android Debug Bridge (ADB)

**Purpose:** Communication between the testing workstation and Android device/emulator.

Useful commands:

```bash
adb devices
```

```bash
adb shell
```

```bash
adb install application.apk
```

```bash
adb uninstall com.example.app
```

```bash
adb logcat
```

```bash
adb shell pm list packages
```

```bash
adb shell dumpsys package com.example.app
```

```bash
adb pull /path/to/file
```

```bash
adb push file /data/local/tmp/
```

---

### Android Studio

Useful for:

* Android emulators
* Logcat
* APK installation
* Device management
* Debugging
* Android SDK management

🔗 https://developer.android.com/studio

---

# 🧬 5. Native Library Analysis

### Ghidra

**Purpose:** Reverse engineering framework for native binaries.

Useful for analyzing:

```text
.so files
Native JNI functions
C/C++ logic
Cryptographic implementations
Native root detection
Native SSL pinning
Anti-debugging
```

Common workflow:

```text
APK
 ↓
Extract lib/*.so
 ↓
Ghidra
 ↓
Analyze native functions
 ↓
Identify security logic
 ↓
Hook with Frida
```

🔗 https://github.com/NationalSecurityAgency/ghidra

---

### IDA Free

Reverse engineering and disassembly tool useful for native Android libraries.

Useful for:

* ARM/ARM64 analysis
* JNI functions
* Native code
* Control-flow analysis

🔗 https://hex-rays.com/ida-free/

---

# 📦 6. APK & Signing Tools

### apksigner

Used to verify and sign Android APKs.

Verify an APK:

```bash
apksigner verify --verbose application.apk
```

Sign an APK:

```bash
apksigner sign --ks my-key.jks application.apk
```

---

### zipalign

Used to optimize APK alignment.

```bash
zipalign -v 4 input.apk output.apk
```

---

# 🧪 7. Emulator & Testing Devices

### Android Emulator

Useful for:

* Controlled testing environments
* Rooted test images
* Different Android versions
* Different CPU architectures
* Automated testing

Common architectures:

```text
x86
x86_64
arm64-v8a
```

---

### Genymotion

Android virtualization platform useful for mobile security testing.

🔗 https://www.genymotion.com/

---

# 🔐 8. Cryptography & Encoding

### CyberChef

Useful for analyzing:

* Base64
* Hex
* URL encoding
* JWTs
* Hashes
* Encryption formats
* Data transformations

🔗 https://gchq.github.io/CyberChef/

---

### OpenSSL

Useful for:

* Certificate analysis
* TLS inspection
* Cryptographic operations
* Key/certificate testing

```bash
openssl s_client -connect example.com:443
```

---

# 🔎 9. Useful Android Commands

### List installed applications

```bash
adb shell pm list packages
```

### Get application path

```bash
adb shell pm path com.example.app
```

### Pull APK

```bash
adb pull /data/app/.../base.apk
```

### View application information

```bash
adb shell dumpsys package com.example.app
```

### Monitor logs

```bash
adb logcat
```

### Search logs

```bash
adb logcat | grep -i "password"
```

### View running processes

```bash
adb shell ps
```

### View current activity

```bash
adb shell dumpsys activity activities
```

---

# 🔬 10. Typical Mobile Pentesting Workflow

```text
                    ┌──────────────────┐
                    │   APK / Mobile   │
                    │   Application    │
                    └────────┬─────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
              ▼                             ▼
       Static Analysis                Dynamic Analysis
              │                             │
      ┌───────┼────────┐             ┌──────┼─────────┐
      │       │        │             │      │         │
    JADX   MobSF   Apktool         Frida  Objection  Drozer
      │       │        │             │      │         │
      └───────┴────────┘             └──────┴─────────┘
              │                             │
              └──────────────┬──────────────┘
                             ▼
                       Traffic Analysis
                             │
                     ┌───────┴───────┐
                     │               │
                  Burp Suite      mitmproxy
                     │               │
                     └───────┬───────┘
                             ▼
                       Vulnerability
                         Assessment
                             │
                             ▼
                          Reporting
```

---

# 🛡️ 11. Main Areas Tested

These tools support testing across the major areas of Android application security:

* 🔐 Authentication
* 👤 Authorization
* 🔑 Session Management
* 🗄️ Insecure Data Storage
* 🌐 API Security
* 🔒 Cryptography
* 📡 Network Communication
* 📱 Android Components
* 🔗 Deep Links
* 📦 APK Security
* 🧬 Native Code
* 🛡️ SSL Pinning
* 🕵️ Root Detection
* 🔍 Debugging Controls
* 🔑 Hardcoded Secrets
* 🧪 WebView Security
* 📋 Clipboard Security
* 📂 File Handling
* 🔄 Exported Components
* ⚙️ IPC Security
* 💳 Business Logic

---

# 📚 Recommended Learning Resources

### Mobile Hacking Lab

https://www.mobilehackinglab.com/

### OWASP Mobile Application Security

https://mas.owasp.org/

### OWASP Android Crackmes

https://mas.owasp.org/crackmes/Android/

### InjuredAndroid

https://github.com/B3nac/InjuredAndroid

### EVABS

https://github.com/abhi-r3v0/EVABS

### Android4 — VulnHub

https://www.vulnhub.com/entry/android4_1,233/

### Mobile-PT — Android Applications

https://github.com/SNGWN/Mobile-PT/tree/master/Applications

---

## ⭐ Recommended Toolkit

If you are starting Android application penetration testing, the core toolkit to become comfortable with is:

```text
ADB
 ↓
JADX
 ↓
MobSF
 ↓
Apktool
 ↓
Burp Suite
 ↓
Frida
 ↓
Objection
 ↓
Ghidra
 ↓
Drozer
```

These tools cover most of the **static analysis, dynamic analysis, API testing, runtime instrumentation, reverse engineering, and Android component testing** required during practical mobile application security assessments.
