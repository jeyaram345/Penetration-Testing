# 📱 Mobile Security Labs

A collection of **Android mobile application security labs, vulnerable applications, crackmes, and CTF challenges** for practicing mobile application penetration testing and reverse engineering.

The labs cover areas such as:

* 🔍 Static Analysis
* 🐞 Dynamic Analysis
* 🔐 Authentication & Authorization
* 💾 Insecure Data Storage
* 🌐 Network Security
* 🔗 Deep Links & Android Components
* 🧩 WebView Security
* 🔑 Cryptography
* 🛡️ Root Detection & SSL Pinning
* 🪝 Frida & Runtime Instrumentation
* ⚙️ Android IPC & Intent Security
* 🧬 Reverse Engineering
* 📦 APK Analysis

---

## 🧪 Practice Labs

### 1. Mobile Hacking Lab

🔗 https://www.mobilehackinglab.com/

A hands-on platform focused specifically on **mobile application security** and practical exploitation.

**Practice areas:**

* Android application security
* Static analysis
* Dynamic analysis
* Frida
* Reverse engineering
* Authentication & authorization
* Native code analysis
* Runtime manipulation

---

### 2. OWASP MAS Crackmes / UnCrackable Apps

🔗 https://mas.owasp.org/crackmes/Android/

OWASP MAS Crackmes are intentionally vulnerable **mobile reverse-engineering challenges** used alongside the OWASP Mobile Application Security Testing Guide (MASTG).

**Challenges:**

* Android UnCrackable L1
* Android UnCrackable L2
* Android UnCrackable L3
* Android UnCrackable L4
* Android License Validator

**Focus:**

* 🔍 Reverse Engineering
* 🪝 Frida
* 🧩 Anti-tampering
* 🔐 Cryptography
* 🧬 Native Code
* 🛠️ Ghidra
* ⚙️ Runtime Analysis

---

### 3. InjuredAndroid

An intentionally vulnerable Android application designed for practicing **Android application penetration testing**.

**Practice areas:**

* Insecure storage
* Exported components
* Activities
* Services
* Broadcast Receivers
* Content Providers
* Deep Links
* Intent vulnerabilities
* Authentication issues
* WebView security
* Runtime analysis

---

### 4. DIVA — Damn Insecure and Vulnerable App

🔗 https://github.com/payatu/diva-android

DIVA is an intentionally insecure Android application containing multiple security challenges. OWASP currently lists DIVA as a reference application for mobile security training.

**Practice areas:**

* Insecure logging
* Hardcoded secrets
* Insecure data storage
* Input validation
* Authentication
* Authorization
* Cryptography
* Android components
* WebViews
* Network security

---

### 5. InsecureBankv2

🔗 https://github.com/dineshshetty/Android-InsecureBankv2

A deliberately vulnerable Android banking application created for learning Android security testing. It is also listed by OWASP MAS as a reference application.

**Practice areas:**

* Authentication
* Authorization
* Insecure storage
* SQL injection
* Exported components
* Intent vulnerabilities
* WebView
* Runtime manipulation
* API security
* Burp Suite testing

---

### 6. AllSafe

A vulnerable Android application designed for practicing **modern Android security testing techniques**.

**Practice areas:**

* Static analysis
* Dynamic analysis
* API security
* Authentication
* Authorization
* Deep Links
* Exported components
* WebViews
* Insecure storage
* SSL/TLS
* Root detection
* Frida

---

### 7. CyberTalents — Mobile Security

🔗 https://cybertalents.com/

Mobile security challenges and CTF-style exercises for developing practical **Android security and reverse-engineering skills**.

**Practice areas:**

* APK analysis
* Reverse engineering
* Authentication bypass
* Data extraction
* Android internals
* Cryptography
* Static analysis
* Dynamic analysis
* CTF-based exploitation

---

## 🛠️ Recommended Toolset

| Tool               | Purpose                            |
| ------------------ | ---------------------------------- |
| **ADB**            | Android Debug Bridge               |
| **JADX**           | Java/Kotlin decompilation          |
| **Apktool**        | APK decoding & rebuilding          |
| **MobSF**          | Automated mobile security analysis |
| **Burp Suite**     | API & network interception         |
| **Frida**          | Dynamic instrumentation            |
| **Objection**      | Runtime mobile security testing    |
| **Ghidra**         | Native binary reverse engineering  |
| **adb shell**      | Android system interaction         |
| **Android Studio** | Emulator & application analysis    |

---

## 🎯 Suggested Learning Progression

### 🟢 Beginner

Start with:

1. DIVA
2. InjuredAndroid
3. InsecureBankv2

Focus on understanding:

* APK structure
* AndroidManifest.xml
* Activities
* Services
* Broadcast Receivers
* Content Providers
* SharedPreferences
* SQLite
* Logcat
* ADB

### 🟡 Intermediate

Move to:

4. AllSafe
5. Mobile Hacking Lab
6. CyberTalents Mobile Security

Focus on:

* Burp Suite
* API testing
* Deep Links
* WebViews
* SSL pinning
* Root detection
* Frida
* Objection
* Runtime manipulation

### 🔴 Advanced

Practice:

7. OWASP MAS Crackmes
8. UnCrackable L1 → L4

Focus on:

* Reverse engineering
* Anti-debugging
* Anti-tampering
* Native libraries
* JNI
* Ghidra
* Frida hooks
* Cryptographic analysis
* Binary patching

---

## 📚 Standards & Methodology

For structured mobile penetration testing, use the **OWASP Mobile Application Security (MAS)** project:

🔗 https://mas.owasp.org/

The project provides:

* **MASVS** — Mobile Application Security Verification Standard
* **MASWE** — Mobile Application Security Weakness Enumeration
* **MASTG** — Mobile Application Security Testing Guide

These provide a useful methodology for conducting consistent mobile application security assessments.

---

## 📋 My Practice Checklist

* [ ] APK Information & Manifest Analysis
* [ ] Static Analysis
* [ ] Dynamic Analysis
* [ ] Authentication Testing
* [ ] Authorization Testing
* [ ] Session Management
* [ ] Insecure Data Storage
* [ ] Cryptography
* [ ] Network Security
* [ ] SSL Pinning
* [ ] Root Detection
* [ ] Anti-Debugging
* [ ] Deep Links
* [ ] Exported Components
* [ ] Intent Security
* [ ] Content Providers
* [ ] Broadcast Receivers
* [ ] Services
* [ ] WebViews
* [ ] API Security
* [ ] Hardcoded Secrets
* [ ] Logging
* [ ] Backup & Debuggable Configuration
* [ ] Frida Instrumentation
* [ ] Native Library Analysis
* [ ] Reverse Engineering
* [ ] Tampering & Repackaging
* [ ] OWASP MASVS/MASTG Mapping

---

> **Goal:** Build practical Android penetration-testing skills through vulnerable applications, CTFs, reverse-engineering challenges, and hands-on security research.
