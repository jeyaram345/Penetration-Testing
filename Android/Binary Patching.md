# 🔧 Binary Patching — From Basics to Advanced

A practical guide to understanding and performing **binary patching** for reverse engineering, vulnerability research, malware analysis, CTFs, and mobile application security testing.

> **Note:** Perform binary modification only on software you own or are explicitly authorized to test.

---

# 📚 Table of Contents

* [1. What is Binary Patching?](#1-what-is-binary-patching)
* [2. Why Binary Patching is Used](#2-why-binary-patching-is-used)
* [3. Binary Fundamentals](#3-binary-fundamentals)
* [4. Hexadecimal Basics](#4-hexadecimal-basics)
* [5. CPU Architecture](#5-cpu-architecture)
* [6. Assembly Basics](#6-assembly-basics)
* [7. Important Binary Concepts](#7-important-binary-concepts)
* [8. Essential Tools](#8-essential-tools)
* [9. Finding the Code to Patch](#9-finding-the-code-to-patch)
* [10. Basic Hex Patching](#10-basic-hex-patching)
* [11. Assembly-Level Patching](#11-assembly-level-patching)
* [12. Conditional Branch Patching](#12-conditional-branch-patching)
* [13. Return-Value Patching](#13-return-value-patching)
* [14. Android APK Patching](#14-android-apk-patching)
* [15. Smali Patching](#15-smali-patching)
* [16. Native ](#16-native-so-patching)[`.so`](#16-native-so-patching)[ Patching](#16-native-so-patching)
* [17. ARM64 Patching](#17-arm64-patching)
* [18. Patching and Rebuilding APKs](#18-patching-and-rebuilding-apks)
* [19. Signing and Verification](#19-signing-and-verification)
* [20. Debugging Patched Binaries](#20-debugging-patched-binaries)
* [21. Common Problems](#21-common-problems)
* [22. Advanced Topics](#22-advanced-topics)
* [23. Practical Workflow](#23-practical-workflow)
* [24. Practice Labs](#24-practice-labs)
* [25. Learning Roadmap](#25-learning-roadmap)

---

# 1. What is Binary Patching?

**Binary patching** is the process of modifying the compiled machine code or data inside a binary executable without changing the original source code.

Instead of:

```text
Source Code
     ↓
Modify Source
     ↓
Compile
     ↓
New Binary
```

Binary patching works directly with:

```text
Existing Binary
      ↓
Analyze
      ↓
Locate Instructions/Data
      ↓
Modify Bytes
      ↓
Patched Binary
```

Example:

```text
Original:

if (authenticated)
    allow();

        ↓

Patched:

if (authenticated)
    allow();

        ↓

Modify conditional branch

        ↓

Patched behavior
```

---

# 2. Why Binary Patching is Used

Binary patching is commonly used in:

### 🔬 Reverse Engineering

Understanding how compiled programs work.

### 🐞 Vulnerability Research

Testing how changing instructions affects application behavior.

### 🧪 CTFs

Modifying challenge binaries to understand or bypass specific logic.

### 📱 Mobile Security

Analyzing and modifying:

* Android APKs
* DEX files
* Smali
* Native `.so` libraries
* JNI code

### 🦠 Malware Analysis

Studying malicious binaries in isolated environments.

### 🧩 Software Research

Understanding compiled applications when source code is unavailable.

---

# 3. Binary Fundamentals

Before patching, understand the basic binary formats.

## Windows

Common formats:

```text
.exe
.dll
.sys
```

These generally use:

```text
PE — Portable Executable
```

## Linux

Common formats:

```text
ELF
```

Examples:

```text
Executable
Shared Object (.so)
```

## Android

Common formats:

```text
APK
DEX
OAT
VDEX
ELF
.so
```

A simplified Android application looks like:

```text
APK
│
├── AndroidManifest.xml
├── classes.dex
├── classes2.dex
├── lib/
│   ├── arm64-v8a/
│   │   └── libnative.so
│   └── x86_64/
│       └── libnative.so
├── res/
└── assets/
```

---

# 4. Hexadecimal Basics

Binary patching requires understanding hexadecimal representation.

Hexadecimal uses:

```text
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

Examples:

```text
Decimal 10 = 0x0A
Decimal 15 = 0x0F
Decimal 16 = 0x10
```

One hexadecimal byte:

```text
00 - FF
```

Therefore:

```text
1 byte = 8 bits
       = 2 hexadecimal digits
```

Example:

```text
48 8B 05 12 34 56 78
```

These are individual bytes.

---

# 5. CPU Architecture

The instructions you patch depend heavily on the CPU architecture.

Common architectures:

```text
x86
x86-64
ARM
ARM32
ARM64 / AArch64
MIPS
RISC-V
```

For Android security testing, the most important are:

```text
ARM64
ARM
x86_64
```

You should always determine the architecture before patching.

Example:

```bash
file binary
```

For an ELF binary:

```bash
readelf -h binary
```

---

# 6. Assembly Basics

A binary ultimately contains machine instructions.

For example, an ARM64 instruction may look conceptually like:

```asm
MOV W0, #1
RET
```

Meaning:

```text
W0 = 1
return
```

A compiler converts source code:

```c
return 1;
```

into machine instructions.

Your reverse-engineering workflow becomes:

```text
Machine Code
     ↓
Disassembler
     ↓
Assembly
     ↓
Understand Logic
     ↓
Patch Instruction
```

---

# 7. Important Binary Concepts

Before patching, understand:

### Registers

Examples:

```text
x0
x1
x2
x3
```

on ARM64.

### Stack

Used for temporary storage and function state.

### Heap

Dynamic memory allocation.

### Functions

Logical blocks of executable code.

### Calls

Instructions that transfer execution to another function.

### Branches

Instructions that change execution flow.

### Return Values

Functions commonly return values through registers.

For ARM64:

```text
x0 / w0
```

is commonly used for return values.

---

# 8. Essential Tools

## Ghidra

Useful for:

* Disassembly
* Decompilation
* Function analysis
* Cross references
* Patching
* Native Android analysis

https://github.com/NationalSecurityAgency/ghidra

---

## IDA Free

Useful for:

* Disassembly
* Control-flow analysis
* Function identification
* Reverse engineering

https://hex-rays.com/ida-free/

---

## Binary Ninja

Useful for:

* Disassembly
* Reverse engineering
* Binary analysis

https://binary.ninja/

---

## radare2

Command-line reverse-engineering framework.

```bash
r2 binary
```

https://github.com/radareorg/radare2

---

## Cutter

GUI for radare2.

https://github.com/rizinorg/cutter

---

## xxd

Useful for viewing binary data as hexadecimal.

```bash
xxd binary
```

---

## objdump

Useful for disassembly.

```bash
objdump -d binary
```

---

## readelf

Useful for ELF analysis.

```bash
readelf -h binary
```

```bash
readelf -S binary
```

```bash
readelf -s binary
```

---

# 9. Finding the Code to Patch

The most important part of binary patching is not changing bytes.

It is **finding the correct instruction**.

A typical workflow:

```text
Application
     ↓
Identify interesting behavior
     ↓
Find related string/function
     ↓
Cross-reference
     ↓
Locate function
     ↓
Understand control flow
     ↓
Identify patch location
```

---

## Searching for Strings

Suppose an application displays:

```text
Authentication Failed
```

Search the binary:

```bash
strings binary | grep -i "Authentication Failed"
```

Then use the result inside a disassembler.

In Ghidra:

```text
Window
 ↓
Defined Strings
 ↓
Search
 ↓
Find String
 ↓
References
```

The references can lead you to the function that uses the string.

---

# 10. Basic Hex Patching

A simple patch changes one or more bytes.

Example conceptual change:

```text
Original:

75 0A

Patched:

74 0A
```

The exact meaning depends on the architecture and instruction encoding.

**Never assume that changing a byte is safe.**

Always determine:

```text
Architecture
Instruction
Instruction length
Control flow
Side effects
```

before modifying it.

---

# 11. Assembly-Level Patching

Suppose the disassembler shows:

```asm
MOV W0, #0
RET
```

Conceptually:

```text
return 0
```

Changing the immediate value could produce:

```asm
MOV W0, #1
RET
```

Conceptually:

```text
return 1
```

This demonstrates an important patching principle:

> Change the smallest possible instruction necessary to produce the desired behavior.

---

# 12. Conditional Branch Patching

A very common reverse-engineering pattern is:

```asm
CMP
B.EQ
```

or:

```asm
CMP
B.NE
```

Conceptually:

```text
if condition == true
    branch
```

You may encounter logic such as:

```text
Check
 ↓
CMP
 ↓
Conditional Branch
 ├── Success
 └── Failure
```

A patch may alter the branch behavior.

Conceptually:

```text
Original:

        Check
          │
      ┌───┴───┐
      │       │
   Failure  Success


Patched:

        Check
          │
          └──────> Success
```

This is commonly encountered during reverse-engineering exercises.

---

# 13. Return-Value Patching

Another common pattern:

```asm
CALL function
MOV result
CMP result
```

The function may return:

```text
0 = failure
1 = success
```

You may encounter:

```asm
MOV W0, #0
RET
```

which conceptually represents:

```c
return 0;
```

Changing the logic to return another value can alter subsequent control flow.

Always verify how the caller interprets the return value.

---

# 14. Android APK Patching

Android patching generally involves several layers.

```text
APK
 │
 ├── DEX
 │    └── Smali
 │
 └── Native Libraries
      └── ELF / .so
```

There are therefore two major patching approaches:

```text
DEX / Smali Patching
```

and:

```text
Native ELF / .so Patching
```

---

# 15. Smali Patching

Smali is the human-readable representation of Android DEX bytecode.

Example:

```smali
.method public isValid()Z
    .locals 1

    const/4 v0, 0x1

    return v0
.end method
```

Conceptually:

```java
boolean isValid() {
    return true;
}
```

Smali patching involves modifying instructions while preserving valid bytecode structure.

---

## Useful Tools

### Apktool

```bash
apktool d app.apk -o app
```

This produces:

```text
app/
├── AndroidManifest.xml
├── smali/
├── res/
└── assets/
```

Modify the required Smali code and rebuild:

```bash
apktool b app -o patched.apk
```

---

# 16. Native `.so` Patching

Many Android applications contain native libraries:

```text
libSomething.so
```

These are ELF binaries.

Typical workflow:

```text
APK
 ↓
Extract .so
 ↓
Identify architecture
 ↓
Load into Ghidra
 ↓
Analyze functions
 ↓
Find target logic
 ↓
Patch instruction
 ↓
Save modified binary
 ↓
Replace .so
 ↓
Rebuild APK
 ↓
Sign APK
 ↓
Install
 ↓
Test
```

---

# 17. ARM64 Patching

ARM64/AArch64 is extremely important for modern Android applications.

Common registers:

```text
X0-X30
W0-W30
SP
PC
```

Examples of common instructions:

```asm
MOV
MOVK
ADD
SUB
CMP
LDR
STR
BL
B
RET
CBZ
CBNZ
B.EQ
B.NE
```

Some common patterns:

```asm
MOV W0, #0
RET
```

Conceptually:

```c
return 0;
```

Another:

```asm
MOV W0, #1
RET
```

Conceptually:

```c
return 1;
```

---

# 18. Patching and Rebuilding APKs

A typical Android workflow:

```text
Original APK
     ↓
apktool decode
     ↓
Modify Smali / resources
     ↓
apktool build
     ↓
zipalign
     ↓
apksigner
     ↓
Install
     ↓
Test
```

Example:

```bash
apktool d app.apk -o app
```

Modify the required files.

Build:

```bash
apktool b app -o patched.apk
```

Align:

```bash
zipalign -v 4 patched.apk patched-aligned.apk
```

Sign:

```bash
apksigner sign --ks test.keystore patched-aligned.apk
```

Verify:

```bash
apksigner verify --verbose patched-aligned.apk
```

---

# 19. Signing and Verification

Android applications normally require valid signing.

After modification, the original signature is no longer valid.

Therefore:

```text
Original APK
     ↓
Modify
     ↓
Original signature invalid
     ↓
Sign with test key
     ↓
Install / test
```

Verify:

```bash
apksigner verify --verbose patched.apk
```

Check certificates:

```bash
apksigner verify --print-certs patched.apk
```

For authorized testing, remember that replacing an application's signing certificate can affect:

* Signature-based permissions
* Certificate pinning
* Play Integrity / integrity checks
* Backend trust relationships
* Application update compatibility

---

# 20. Debugging Patched Binaries

A patch that compiles successfully does not necessarily work correctly.

Test systematically.

### Check installation

```bash
adb install patched.apk
```

### Monitor crashes

```bash
adb logcat
```

### Filter application logs

```bash
adb logcat | grep -i "AndroidRuntime"
```

### Check process

```bash
adb shell ps
```

### Inspect the current activity

```bash
adb shell dumpsys activity activities
```

---

# 21. Common Problems

## ❌ APK Does Not Install

Possible causes:

```text
Invalid signature
APK not aligned
Corrupted APK
Unsupported SDK
Architecture mismatch
```

Check:

```bash
apksigner verify --verbose patched.apk
```

---

## ❌ Application Crashes

Possible causes:

```text
Invalid Smali
Incorrect instruction patch
Wrong register usage
Broken control flow
Native library corruption
ABI mismatch
```

Check:

```bash
adb logcat
```

---

## ❌ Native Library Does Not Load

Possible causes:

```text
Wrong architecture
Invalid ELF
Modified program headers
Incorrect instruction encoding
Library dependencies
```

Check:

```bash
file libSomething.so
```

and:

```bash
readelf -h libSomething.so
```

---

## ❌ Patch Has No Effect

Possible causes:

```text
Wrong function
Wrong code path
Dead code
Different architecture
Multiple copies of the function
Runtime-generated behavior
Server-side validation
```

Always confirm execution with a debugger or instrumentation framework.

---

# 22. Advanced Topics

Once you understand basic patching, progress to:

### Control-Flow Analysis

Understand:

```text
Basic Blocks
Control Flow Graphs
Branches
Loops
Function Calls
```

### Function Prologue / Epilogue

Understand how functions establish and restore their stack frames.

### Calling Conventions

Learn how:

```text
Arguments
Return values
Registers
Stack
```

are handled by the target architecture.

### Position Independent Code

Important for modern shared libraries.

### ASLR

Understand:

```text
Address Space Layout Randomization
```

and why runtime addresses differ from static addresses.

### PIE

Understand:

```text
Position Independent Executable
```

### Relocations

Important when working with ELF binaries.

### PLT / GOT

Important for understanding dynamically linked functions.

### JNI

Extremely important for Android native applications.

```text
Java / Kotlin
      ↓
     JNI
      ↓
 C / C++
      ↓
    .so
```

---

# 23. Practical Workflow

A good binary-patching workflow is:

```text
                ┌─────────────────┐
                │ Target Binary   │
                └────────┬────────┘
                         │
                         ▼
                Identify Architecture
                         │
                         ▼
                  Static Analysis
                         │
                  ┌──────┴──────┐
                  ▼             ▼
               Strings       Functions
                  │             │
                  └──────┬──────┘
                         ▼
                  Cross References
                         │
                         ▼
                   Control Flow
                         │
                         ▼
                  Identify Target
                         │
                         ▼
                  Understand Code
                         │
                         ▼
                    Patch Bytes
                         │
                         ▼
                  Verify Instruction
                         │
                         ▼
                  Rebuild / Save
                         │
                         ▼
                      Execute
                         │
                         ▼
                     Debug
                         │
                         ▼
                   Verify Behavior
```

---

# 24. Practice Labs

Start with intentionally vulnerable or educational binaries.

## Beginner

Practice:

```text
Strings
Hexadecimal
Disassembly
Registers
Simple return values
Basic conditional branches
```

Recommended:

* Crackmes
* CTF reversing challenges
* OWASP crackmes
* Beginner reverse-engineering challenges

---

## Intermediate

Practice:

```text
Function identification
Cross references
Control-flow graphs
Smali modification
DEX analysis
ELF analysis
ARM64 instructions
```

---

## Advanced

Practice:

```text
Native Android libraries
JNI
ARM64 reversing
Ghidra patching
Dynamic validation
Frida + static analysis
Anti-debugging research
Obfuscation analysis
```

---

# 25. Learning Roadmap

## Level 1 — Foundations

Learn:

```text
Binary vs hexadecimal
Bits and bytes
CPU architecture
Registers
Memory
Stack / Heap
Assembly fundamentals
```

⬇️

## Level 2 — Reverse Engineering

Learn:

```text
Ghidra
JADX
IDA
radare2
Strings
Cross references
Functions
Control-flow graphs
```

⬇️

## Level 3 — Basic Patching

Practice:

```text
NOP
Return-value changes
Constant changes
Conditional branches
Simple instruction replacement
```

⬇️

## Level 4 — Android Patching

Learn:

```text
APK structure
DEX
Smali
Apktool
APK rebuilding
zipalign
apksigner
```

⬇️

## Level 5 — Native Android

Learn:

```text
ELF
ARM64
JNI
.so libraries
Ghidra
Native functions
Calling conventions
```

⬇️

## Level 6 — Advanced Research

Learn:

```text
Dynamic analysis
Frida
Debugging
Anti-debugging
Obfuscation
ASLR
PIE
RELRO
GOT / PLT
Control-flow analysis
```

---

# 🧰 Recommended Toolkit

```text
                 Binary Patching Toolkit
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
       ▼                 ▼                 ▼
   Static Analysis   Dynamic Analysis   Android
       │                 │                 │
   ┌───┼───┐         ┌───┼───┐        ┌───┼────┐
   │   │   │         │   │   │        │   │    │
Ghidra IDA r2      GDB Frida x64dbg  JADX Apktool ADB
```

### Core Tools

* **Ghidra** — Reverse engineering & patching
* **IDA** — Disassembly & analysis
* **radare2/Cutter** — CLI/GUI binary analysis
* **JADX** — Android DEX analysis
* **Apktool** — APK/Smali modification
* **ADB** — Android device interaction
* **Frida** — Dynamic instrumentation
* **GDB** — Debugging
* **x64dbg** — Windows user-mode debugging

---

# 🎯 Key Principle

Binary patching is not simply:

```text
Find byte → Change byte
```

A professional workflow is:

```text
Understand
    ↓
Locate
    ↓
Analyze
    ↓
Patch
    ↓
Validate
    ↓
Debug
    ↓
Document
```

The better you understand **assembly, CPU architecture, calling conventions, executable formats, and control flow**, the more reliably you can patch binaries.
