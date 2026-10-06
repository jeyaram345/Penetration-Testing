# 📱 Mobile Security Labs

A collection of **Android mobile application security labs, vulnerable applications, CTFs, crackmes, and hands-on practice resources** for learning Mobile Application Penetration Testing.

These labs are useful for practicing:

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
* 🧠 Native Library Analysis
* 🚩 CTF & Flag-Based Challenges

---

# 🧪 Practice Labs

## 1. Mobile Hacking Lab

🔗 https://www.mobilehackinglab.com/

A hands-on platform focused on **mobile application security testing and Android exploitation**.

### Practice Areas

* Android application security
* Static analysis
* Dynamic analysis
* Frida
* Reverse engineering
* Authentication & authorization
* API security
* Native code analysis
* Runtime manipulation
* SSL pinning
* Root detection

---

## 2. OWASP MAS Crackmes / UnCrackable Apps

🔗 https://mas.owasp.org/crackmes/Android/

OWASP MAS Crackmes provide intentionally vulnerable Android applications and reverse-engineering challenges for practicing mobile security testing.

### Practice Areas

* 🔍 Reverse Engineering
* 🪝 Frida
* 🧩 Anti-tampering
* 🔐 Cryptography
* 🧬 Native Code
* 🛠️ Ghidra
* ⚙️ Runtime Analysis
* 🔓 Authentication Bypass

### Challenges

* Android UnCrackable L1
* Android UnCrackable L2
* Android UnCrackable L3
* Android UnCrackable L4
* Android License Validator

---

## 3. InjuredAndroid

An intentionally vulnerable Android application created for practicing **Android application penetration testing**.

### Practice Areas

* Insecure Data Storage
* Exported Components
* Activities
* Services
* Broadcast Receivers
* Content Providers
* Deep Links
* Intent Security
* Authentication
* Authorization
* WebView Security
* Runtime Analysis
* API Testing

---

## 4. DIVA — Damn Insecure and Vulnerable App

🔗 https://github.com/payatu/diva-android

DIVA is an intentionally vulnerable Android application designed for learning common Android security weaknesses.

### Practice Areas

* Insecure Logging
* Hardcoded Secrets
* Insecure Data Storage
* Input Validation
* Authentication
* Authorization
* Cryptography
* Android Components
* WebViews
* Network Security

---

## 5. InsecureBankv2

🔗 https://github.com/dineshshetty/Android-InsecureBankv2

An intentionally vulnerable Android banking application for practicing **mobile application and API security testing**.

### Practice Areas

* Authentication
* Authorization
* Insecure Storage
* SQL Injection
* Exported Components
* Intent Vulnerabilities
* WebView Security
* Runtime Manipulation
* API Security
* Burp Suite Testing

---

## 6. AllSafe

An intentionally vulnerable Android application useful for practicing **modern Android security testing techniques**.

### Practice Areas

* Static Analysis
* Dynamic Analysis
* API Security
* Authentication
* Authorization
* Deep Links
* Exported Components
* WebViews
* Insecure Storage
* SSL/TLS
* Root Detection
* Frida
* Runtime Instrumentation

---

## 7. EVABS — Extremely Vulnerable Android Labs

🔗 https://github.com/abhi-r3v0/EVABS

**EVABS (Extremely Vulnerable Android Labs)** is an intentionally vulnerable Android application designed as a learning platform for Android application security beginners. It also includes CTF-style flag challenges.

### Practice Areas

* 🔍 Static Analysis
* 🐞 Dynamic Analysis
* 🔐 Authentication
* 💾 Insecure Storage
* 🔗 Android Components
* 🧩 Intent Security
* 🔑 Cryptography
* 🪝 Frida
* 🧬 Reverse Engineering
* 🚩 CTF / Flag Challenges
* 📦 APK Analysis

### Recommended Tools

* ADB
* Frida
* Apktool
* JADX
* dex2jar
* Android Studio

---

## 8. Android4 — VulnHub

🔗 https://www.vulnhub.com/entry/android4_1,233/

**Android4** is a vulnerable Android-based machine available through VulnHub and can be used for practicing Android security assessment and exploitation in a controlled lab environment.

### Practice Areas

* Android Enumeration
* Network Enumeration
* Service Discovery
* Android Security
* Exploitation
* Privilege Escalation
* Reverse Engineering
* CTF-Style Challenges

> ⚠️ Run VulnHub machines only inside an isolated and authorized lab environment.

---

## 9. CyberTalents — Mobile Security

🔗 https://cybertalents.com/

CyberTalents provides **CTF-style cybersecurity challenges**, including mobile security and Android-related challenges.

### Practice Areas

* APK Analysis
* Reverse Engineering
* Authentication Bypass
* Data Extraction
* Android Internals
* Cryptography
* Static Analysis
* Dynamic Analysis
* CTF Challenges

---

# 🛠️ Recommended Mobile Pentesting Tools

| Tool               | Purpose                            |
| ------------------ | ---------------------------------- |
| **ADB**            | Android Debug Bridge               |
| **JADX**           | APK/DEX decompilation              |
| **Apktool**        | APK decoding & rebuilding          |
| **MobSF**          | Automated mobile security analysis |
| **Burp Suite**     | HTTP/HTTPS & API testing           |
| **Frida**          | Dynamic instrumentation            |
| **Objection**      | Runtime mobile security testing    |
| **Ghidra**         | Native binary reverse engineering  |
| **dex2jar**        | DEX → JAR conversion               |
| **adb shell**      | Android system interaction         |
| **Android Studio** | Emulator & application analysis    |

---

# 🎯 Recommended Learning Path

## 🟢 Beginner

Start with:

1. **DIVA**
2. **InjuredAndroid**
3. **InsecureBankv2**
4. **EVABS**

Focus on:

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
* Basic static analysis

---

## 🟡 Intermediate

Move to:

5. **AllSafe**
6. **Mobile Hacking Lab**
7. **CyberTalents Mobile Security**

Focus on:

* Burp Suite
* API testing
* Deep Links
* WebViews
* SSL Pinning
* Root Detection
* Frida
* Objection
* Runtime Manipulation
* Android Components

---

## 🔴 Advanced

Practice:

8. **OWASP MAS Crackmes**
9. **UnCrackable Series**
10. **Android4 / VulnHub**

Focus on:

* Reverse Engineering
* Anti-Debugging
* Anti-Tampering
* Native Libraries
* JNI
* Ghidra
* Frida Hooks
* Cryptographic Analysis
* Binary Patching
* Exploitation

---

# 📚 Methodology & Standards

For a structured approach to mobile application security testing:

### OWASP Mobile Application Security

🔗 https://mas.owasp.org/

Study:

* **MASVS** — Mobile Application Security Verification Standard
* **MASWE** — Mobile Application Security Weakness Enumeration
* **MASTG** — Mobile Application Security Testing Guide

Use these standards to map vulnerabilities discovered during your lab exercises to industry-recognized security requirements.

---

# 📋 Mobile Pentesting Practice Checklist

### 🔍 Reconnaissance & Static Analysis

* [ ] APK Information
* [ ] Package Name
* [ ] AndroidManifest.xml
* [ ] Permissions
* [ ] Exported Components
* [ ] Activities
* [ ] Services
* [ ] Broadcast Receivers
* [ ] Content Providers
* [ ] Hardcoded Secrets
* [ ] API Endpoints
* [ ] Debuggable Configuration
* [ ] Backup Configuration

### 🔐 Authentication & Authorization

* [ ] Authentication Bypass
* [ ] Authorization Bypass
* [ ] Broken Access Control
* [ ] Session Management
* [ ] Password Policy
* [ ] Account Enumeration
* [ ] Token Handling
* [ ] Logout Validation

### 💾 Data Storage

* [ ] SharedPreferences
* [ ] SQLite Databases
* [ ] Internal Storage
* [ ] External Storage
* [ ] Cache
* [ ] Logs
* [ ] Clipboard
* [ ] Sensitive Data Exposure
* [ ] Encryption at Rest

### 🌐 Network Security

* [ ] HTTP/HTTPS
* [ ] TLS Configuration
* [ ] Certificate Validation
* [ ] SSL Pinning
* [ ] API Security
* [ ] Request Manipulation
* [ ] Response Manipulation
* [ ] Authentication Tokens

### 🔗 Android Components

* [ ] Exported Activities
* [ ] Exported Services
* [ ] Exported Receivers
* [ ] Exported Content Providers
* [ ] Intent Injection
* [ ] Intent Redirection
* [ ] Deep Links
* [ ] App Links
* [ ] URI Handling

### 🧩 WebView

* [ ] JavaScript Enabled
* [ ] JavaScript Interfaces
* [ ] Unsafe URL Loading
* [ ] File Access
* [ ] Universal Access
* [ ] URL Validation
* [ ] WebView Injection

### 🪝 Runtime & Reverse Engineering

* [ ] Frida
* [ ] Objection
* [ ] Root Detection
* [ ] Anti-Debugging
* [ ] Anti-Frida
* [ ] SSL Pinning Bypass
* [ ] Runtime Hooking
* [ ] Native Library Analysis
* [ ] JNI Analysis
* [ ] Ghidra
* [ ] Binary Patching
* [ ] Repackaging

---

# 📂 Additional Mobile Pentesting Resources

For an additional collection of vulnerable applications, tools, scripts, and mobile penetration-testing resources:

🔗 **Mobile-PT — Applications**
https://github.com/SNGWN/Mobile-PT/tree/master/Applications

The repository's `Applications/` directory contains sample vulnerable applications for mobile security testing, including DIVA, InsecureBankv2, UnCrackable challenges, GoatDroid and other testing applications.

---

> **Goal:** Build practical Android penetration-testing skills through vulnerable applications, CTF challenges, reverse-engineering exercises, runtime instrumentation, and systematic security testing using OWASP MASVS/MASTG.

> ⚠️ **Disclaimer:** Use these applications and labs only for educational purposes and authorized security testing. Never test systems or applications without permission.
