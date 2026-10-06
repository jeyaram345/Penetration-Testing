# 🐉 Ghidra for Mobile Application Pentesting

A complete beginner-to-advanced guide to using **Ghidra for Android and mobile application security testing**.

Ghidra is a powerful reverse-engineering framework that can be used to analyze:

* Android native `.so` libraries
* ELF binaries
* ARM / ARM64 binaries
* x86 / x86_64 binaries
* C/C++ native code
* JNI implementations
* Native authentication logic
* Cryptographic implementations
* Root detection
* Anti-debugging mechanisms
* Certificate validation
* Security checks
* Native APIs
* Control flow
* Memory-related behavior
* Native vulnerability research

This guide focuses specifically on using Ghidra as part of an **authorized mobile application penetration-testing workflow**.

> **Scope:** Use Ghidra against applications and binaries that you own, are authorized to assess, or are intentionally provided for security research, CTFs, crackmes, and training.

---

# 📚 Table of Contents

* [1. What is Ghidra?](#1-what-is-ghidra)
* [2. Why Ghidra for Mobile Pentesting?](#2-why-ghidra-for-mobile-pentesting)
* [3. Ghidra in Android Architecture](#3-ghidra-in-android-architecture)
* [4. Prerequisites](#4-prerequisites)
* [5. Installation](#5-installation)
* [6. Ghidra Directory Structure](#6-ghidra-directory-structure)
* [7. Starting Ghidra](#7-starting-ghidra)
* [8. Creating a Project](#8-creating-a-project)
* [9. Ghidra Interface](#9-ghidra-interface)
* [10. Importing a Binary](#10-importing-a-binary)
* [11. Understanding ELF](#11-understanding-elf)
* [12. Android Native Libraries](#12-android-native-libraries)
* [13. Extracting .so Files from APK](#13-extracting-so-files-from-apk)
* [14. Identifying Architecture](#14-identifying-architecture)
* [15. Auto Analysis](#15-auto-analysis)
* [16. Program Listing](#16-program-listing)
* [17. Symbol Tree](#17-symbol-tree)
* [18. Functions](#18-functions)
* [19. Labels and Renaming](#19-labels-and-renaming)
* [20. Strings](#20-strings)
* [21. Cross References (XREF)](#21-cross-references-xref)
* [22. Decompiler](#22-decompiler)
* [23. Disassembly](#23-disassembly)
* [24. ARM64 Basics](#24-arm64-basics)
* [25. ARM64 Registers](#25-arm64-registers)
* [26. ARM64 Instructions](#26-arm64-instructions)
* [27. Function Calls](#27-function-calls)
* [28. Conditional Branches](#28-conditional-branches)
* [29. Return Values](#29-return-values)
* [30. JNI Reverse Engineering](#30-jni-reverse-engineering)
* [31. Finding JNI Functions](#31-finding-jni-functions)
* [32. RegisterNatives](#32-registernatives)
* [33. Native Security Checks](#33-native-security-checks)
* [34. Root Detection Analysis](#34-root-detection-analysis)
* [35. Anti-Debugging Analysis](#35-anti-debugging-analysis)
* [36. TLS and Certificate Validation](#36-tls-and-certificate-validation)
* [37. Cryptographic Analysis](#37-cryptographic-analysis)
* [38. Hardcoded Secrets](#38-hardcoded-secrets)
* [39. Authentication Logic](#39-authentication-logic)
* [40. Authorization Logic](#40-authorization-logic)
* [41. API Endpoint Analysis](#41-api-endpoint-analysis)
* [42. Control-Flow Analysis](#42-control-flow-analysis)
* [43. Ghidra Graphs](#43-ghidra-graphs)
* [44. Data Flow Analysis](#44-data-flow-analysis)
* [45. Function Call Trees](#45-function-call-trees)
* [46. Structures and Data Types](#46-structures-and-data-types)
* [47. Memory Analysis Concepts](#47-memory-analysis-concepts)
* [48. Ghidra and ADB](#48-ghidra-and-adb)
* [49. Ghidra and Frida](#49-ghidra-and-frida)
* [50. Static → Dynamic Workflow](#50-static--dynamic-workflow)
* [51. Native Binary Patching](#51-native-binary-patching)
* [52. Ghidra Patch Workflow](#52-ghidra-patch-workflow)
* [53. Common Ghidra Analysis Mistakes](#53-common-ghidra-analysis-mistakes)
* [54. Testing Technique vs Finding](#54-testing-technique-vs-finding)
* [55. Ghidra Technique → Finding Mapping](#55-ghidra-technique--finding-mapping)
* [56. Complete Beginner Practice Lab](#56-complete-beginner-practice-lab)
* [57. Intermediate Practice](#57-intermediate-practice)
* [58. Advanced Practice](#58-advanced-practice)
* [59. Ghidra Cheat Sheet](#59-ghidra-cheat-sheet)
* [60. Mobile Reverse Engineering Workflow](#60-mobile-reverse-engineering-workflow)
* [61. Learning Roadmap](#61-learning-roadmap)
* [62. Final Checklist](#62-final-checklist)
* [63. Responsible Use](#63-responsible-use)

---

# 1. What is Ghidra?

[Ghidra](https://ghidra-sre.org/) is a software reverse-engineering framework developed by the NSA Research Directorate and released as open source.

It provides capabilities for:

```text
Disassembly
Decompilation
Debugging
Binary analysis
Function identification
String analysis
Cross-reference analysis
Control-flow analysis
Data-flow analysis
Scripting
Binary modification
```

For Android security testing, Ghidra is particularly useful when an application contains native libraries:

```text
APK
 │
 └── lib/
      └── arm64-v8a/
           └── libnative.so
```

---

# 2. Why Ghidra for Mobile Pentesting?

Android applications are not always completely implemented in Java/Kotlin.

A simplified application may look like:

```text
┌───────────────────────────────┐
│       Android Application     │
├───────────────────────────────┤
│ Java / Kotlin                 │
├───────────────────────────────┤
│ Android Framework             │
├───────────────────────────────┤
│ JNI                           │
├───────────────────────────────┤
│ C / C++                       │
├───────────────────────────────┤
│ Native .so Libraries          │
├───────────────────────────────┤
│ Android/Linux Kernel          │
└───────────────────────────────┘
```

JADX is excellent for Java/Kotlin-oriented analysis.

Ghidra becomes important when the application uses:

```text
C
C++
JNI
Native libraries
ARM64
Native cryptography
Native security controls
```

---

# 3. Ghidra in Android Architecture

A common architecture:

```text
Java/Kotlin
     │
     │ System.loadLibrary()
     ▼
JNI
     │
     ▼
C/C++
     │
     ▼
libnative.so
     │
     ▼
ARM64 CPU
```

For example:

```java
public native boolean checkValue(String value);
```

The actual implementation may exist inside:

```text
libnative.so
```

Ghidra allows us to investigate that native implementation.

---

# 4. Prerequisites

You do **not** need to be an expert in assembly before starting.

### Required

Basic knowledge of:

* Android APK structure
* ADB
* Linux/Windows command line
* Java/Kotlin basics
* C/C++ fundamentals
* HTTP/API basics

### Recommended

Learn:

```text
Hexadecimal
Binary
Pointers
Memory
Functions
Stacks
Registers
Assembly
ELF
ARM64
JNI
```

### Android tools

Recommended:

```text
ADB
Android Studio
Android Emulator
JADX
apktool
```

### Ghidra knowledge

Start with:

```text
Projects
Importing
Auto Analysis
Functions
Strings
XREF
Decompiler
Disassembly
```

Then progress to:

```text
ARM64
JNI
Control Flow
Data Flow
Native Security
Patching
Scripting
```

---

# 5. Installation

Download Ghidra from the official project:

https://ghidra-sre.org/

You will generally need a compatible Java runtime/JDK according to the Ghidra release requirements.

Verify Java:

```bash
java -version
```

Extract Ghidra.

Linux:

```bash
unzip ghidra_*.zip
cd ghidra_*
```

Start:

```bash
./ghidraRun
```

Windows:

```cmd
ghidraRun.bat
```

---

# 6. Ghidra Directory Structure

A typical installation contains:

```text
ghidra/
├── Ghidra/
├── docs/
├── Extensions/
├── licenses/
├── GPL/
├── server/
├── support/
└── ghidraRun
```

You normally start Ghidra using:

```text
ghidraRun
```

or:

```text
ghidraRun.bat
```

---

# 7. Starting Ghidra

When Ghidra starts, you will see the:

```text
Ghidra Project Window
```

This is where you manage projects and imported programs.

Basic workflow:

```text
Start Ghidra
     ↓
Create Project
     ↓
Import Binary
     ↓
Analyze
     ↓
Open CodeBrowser
```

---

# 8. Creating a Project

Create a new project:

```text
File
 ↓
New Project
```

Choose:

```text
Non-Shared Project
```

Choose a project directory.

Example:

```text
GhidraProjects/
└── AndroidReverseEngineering/
```

---

# 9. Ghidra Interface

The primary interface is **CodeBrowser**.

Important windows:

```text
┌────────────────────────────────────┐
│ Menu / Toolbar                     │
├───────────────┬────────────────────┤
│ Symbol Tree   │ Listing            │
│               │                    │
│ Functions     │ Assembly           │
│ Labels        │                    │
│ Imports       │                    │
│ Exports       │                    │
├───────────────┴────────────────────┤
│ Decompiler                         │
│ C-like representation              │
└────────────────────────────────────┘
```

Important areas:

### Symbol Tree

Contains:

```text
Functions
Labels
Imports
Exports
Classes
Namespaces
```

### Listing

Shows:

```text
Addresses
Instructions
Data
Bytes
Labels
```

### Decompiler

Shows reconstructed C-like code.

---

# 10. Importing a Binary

From the project window:

```text
File
 ↓
Import File
```

Select:

```text
libnative.so
```

Ghidra should detect the format.

For Android native libraries, this is commonly:

```text
ELF
```

Verify:

```text
Format: ELF
Language: ARM64
Compiler: ...
```

Then click:

```text
OK
```

---

# 11. Understanding ELF

Android native `.so` libraries are generally ELF binaries.

ELF means:

> Executable and Linkable Format

Basic structure:

```text
ELF
├── ELF Header
├── Program Headers
├── Section Headers
├── Code
├── Data
├── Symbols
└── Dynamic Information
```

Useful command:

```bash
readelf -h libnative.so
```

View sections:

```bash
readelf -S libnative.so
```

View symbols:

```bash
readelf -s libnative.so
```

View dynamic information:

```bash
readelf -d libnative.so
```

---

# 12. Android Native Libraries

Native libraries are commonly located inside:

```text
lib/
```

Possible architectures:

```text
arm64-v8a
armeabi-v7a
x86
x86_64
```

Example:

```text
lib/
└── arm64-v8a/
    ├── libnative.so
    ├── libcrypto.so
    └── libsecurity.so
```

---

# 13. Extracting .so Files from APK

An APK can be treated as a ZIP archive.

```bash
unzip application.apk -d extracted/
```

Find native libraries:

```bash
find extracted/ -name "*.so"
```

Windows:

```cmd
dir /s /b extracted\*.so
```

Alternative:

```bash
unzip -l application.apk | grep "\.so"
```

Example result:

```text
lib/arm64-v8a/libnative.so
```

Extract:

```bash
unzip application.apk "lib/*" -d native/
```

---

# 14. Identifying Architecture

Use:

```bash
file libnative.so
```

Example:

```text
ELF 64-bit LSB shared object, ARM aarch64
```

You can also use:

```bash
readelf -h libnative.so
```

Look for:

```text
Class
Data
Machine
Entry point
```

Architecture matters because the instruction set changes.

---

# 15. Auto Analysis

After importing a binary, Ghidra asks whether to analyze it.

Select:

```text
Yes
```

The analysis engine can identify:

```text
Functions
Strings
References
Instructions
Control flow
Symbols
Data
```

For beginners:

> Start with the default analysis options.

As you become advanced, learn what each analyzer does.

---

# 16. Program Listing

The Listing window is where you see the actual binary representation.

Example:

```asm
00101230    MOV W0, #0x1
00101234    RET
```

You may also see:

```text
Addresses
Bytes
Labels
Instructions
Operands
Comments
```

This is closer to the actual compiled program than the decompiler.

---

# 17. Symbol Tree

The Symbol Tree is one of the most important areas.

Expand:

```text
Functions
```

You may see:

```text
FUN_00101230
FUN_00101320
FUN_00101480
```

If symbols were preserved, you may instead see meaningful names:

```text
Java_com_example_MainActivity_check
JNI_OnLoad
strcmp
memcpy
```

---

# 18. Functions

A function represents a logical unit of executable code.

Example:

```c
int checkPassword(char *input)
{
    ...
}
```

Ghidra may initially show:

```text
FUN_00101230
```

Your task is to determine what it does.

Useful questions:

```text
What arguments does it receive?
What functions does it call?
What data does it access?
What does it return?
Where is it called?
```

---

# 19. Labels and Renaming

Suppose you discover:

```text
FUN_00101230
```

checks whether a value is a root indicator.

You can rename it:

```text
checkRoot
```

Meaningful naming makes large projects much easier to understand.

Good:

```text
checkRoot
validateToken
decryptData
verifyCertificate
isDebuggerAttached
```

Bad:

```text
function1
test
abc
thing
```

Only assign a meaningful name when your analysis supports it.

---

# 20. Strings

Strings are one of the easiest ways to start analyzing an unknown binary.

Open:

```text
Window
 ↓
Defined Strings
```

Search for:

```text
root
su
magisk
frida
debug
ptrace
certificate
ssl
tls
password
token
secret
encrypt
decrypt
admin
error
```

You may find:

```text
"root detected"
"certificate verification failed"
"invalid token"
"debugger detected"
```

But:

```text
Interesting string
      ≠
Vulnerability
```

Always follow the XREF.

---

# 21. Cross References (XREF)

XREF tells you where something is referenced.

Suppose you find:

```text
"root detected"
```

Follow its references.

Conceptually:

```text
String
  ↓
XREF
  ↓
Function
  ↓
Decompiler
  ↓
Security Decision
```

This is one of the most important Ghidra workflows.

---

# 22. Decompiler

The decompiler converts machine instructions into a C-like representation.

Example:

```c
int checkValue(int value)
{
    if (value == 1) {
        return 1;
    }

    return 0;
}
```

The actual binary may look like:

```asm
CMP W0, #1
B.NE ...
MOV W0, #1
RET
```

The decompiler makes the logic easier to understand.

### Important

Decompiler output is **not the original source code**.

It may contain:

* Incorrect variable names
* Incorrect data types
* Simplified logic
* Missing semantics
* Compiler artifacts

For critical conclusions:

```text
Decompiler
    +
Disassembly
    +
Runtime behavior
```

---

# 23. Disassembly

Disassembly converts machine code into assembly instructions.

Example:

```asm
MOV W0, #1
RET
```

This can represent:

```c
return 1;
```

But always examine the function context.

---

# 24. ARM64 Basics

Android devices commonly use ARM64.

ARM64 is also called:

```text
AArch64
```

A basic instruction sequence:

```asm
MOV X0, X1
ADD X0, X0, X2
RET
```

Conceptually:

```text
X0 = X1
X0 = X0 + X2
return
```

---

# 25. ARM64 Registers

Important registers:

```text
X0-X30
W0-W30
SP
PC
```

### X registers

64-bit general-purpose registers.

```text
X0
X1
X2
...
X30
```

### W registers

Lower 32 bits of the corresponding X register.

```text
W0
W1
W2
...
```

### SP

Stack Pointer.

### PC

Program Counter.

---

# 26. ARM64 Instructions

Important instructions:

| Instruction | Purpose                |
| ----------- | ---------------------- |
| `MOV`       | Move value             |
| `MOVK`      | Modify immediate value |
| `ADD`       | Addition               |
| `SUB`       | Subtraction            |
| `MUL`       | Multiplication         |
| `CMP`       | Comparison             |
| `LDR`       | Load                   |
| `STR`       | Store                  |
| `BL`        | Function call          |
| `B`         | Branch                 |
| `RET`       | Return                 |
| `CBZ`       | Branch if zero         |
| `CBNZ`      | Branch if non-zero     |
| `B.EQ`      | Branch if equal        |
| `B.NE`      | Branch if not equal    |

---

# 27. Function Calls

In ARM64, function arguments are commonly passed through:

```text
X0
X1
X2
X3
...
```

For example:

```c
checkValue(a, b);
```

may conceptually use:

```text
X0 = a
X1 = b
```

A function call:

```asm
BL checkValue
```

After the call, the return value is commonly found in:

```text
X0 / W0
```

---

# 28. Conditional Branches

Security decisions often contain conditional branches.

Example:

```asm
CMP W0, #0
B.EQ failure
```

Conceptually:

```c
if (result == 0) {
    goto failure;
}
```

Another example:

```asm
CMP W0, #1
B.NE failure
```

Conceptually:

```c
if (result != 1) {
    goto failure;
}
```

This is extremely useful when analyzing:

```text
Authentication
Root detection
Debug detection
Certificate validation
License checks
Feature restrictions
```

---

# 29. Return Values

Consider:

```asm
MOV W0, #1
RET
```

This commonly represents:

```c
return 1;
```

And:

```asm
MOV W0, #0
RET
```

commonly represents:

```c
return 0;
```

This pattern is useful for understanding functions such as:

```text
isRooted()
isDebugged()
isValid()
isAuthenticated()
isAdmin()
checkCertificate()
```

But do not assume the meaning without examining how the caller uses the result.

---

# 30. JNI Reverse Engineering

JNI stands for:

> Java Native Interface

It allows Java/Kotlin code to communicate with native C/C++ code.

Example Java:

```java
public native boolean checkValue(String value);
```

Native implementation may be inside:

```text
libnative.so
```

Architecture:

```text
Java
 │
 ▼
Native Method
 │
 ▼
JNI
 │
 ▼
C/C++
 │
 ▼
ARM64
```

---

# 31. Finding JNI Functions

Search Ghidra strings/functions for:

```text
JNI_OnLoad
Java_
RegisterNatives
```

Example naming:

```text
Java_com_example_MainActivity_checkValue
```

This can reveal:

```text
Package
Class
Method
```

For example:

```text
Java_com_example_MainActivity_checkValue
```

may correspond to:

```java
com.example.MainActivity.checkValue()
```

---

# 32. RegisterNatives

Some applications do not expose JNI functions using obvious `Java_...` names.

Instead they use:

```text
RegisterNatives
```

Conceptually:

```text
Java/Kotlin Method
       │
       ▼
Native Registration Table
       │
       ▼
Native Function
```

When you encounter:

```text
RegisterNatives
```

trace the native method registration structures and function pointers.

This is particularly important in applications attempting to make native functions harder to identify.

---

# 33. Native Security Checks

Ghidra can help investigate:

```text
Root Detection
Anti-Debugging
TLS Validation
Certificate Validation
Cryptography
Authentication
Authorization
License Validation
Environment Detection
```

A useful process:

```text
Search
  ↓
String
  ↓
XREF
  ↓
Function
  ↓
Decompiler
  ↓
Assembly
  ↓
Caller
  ↓
Security Decision
```

---

# 34. Root Detection Analysis

Search for:

```text
su
/system/bin/su
/system/xbin/su
magisk
busybox
root
```

Also investigate native functions interacting with:

```text
File system
Process information
System properties
Executable paths
```

Possible logic:

```c
if (file_exists("/system/bin/su")) {
    return true;
}

return false;
```

The important question is:

> Does this root-detection result actually enforce a meaningful security boundary?

A root check existing in the binary is not automatically a vulnerability.

---

# 35. Anti-Debugging Analysis

Search for:

```text
ptrace
TracerPid
debugger
/proc/
```

Possible logic:

```c
if (debugger_detected()) {
    terminate_application();
}
```

Trace:

```text
debugger_detected()
       ↓
return value
       ↓
caller
       ↓
security decision
```

---

# 36. TLS and Certificate Validation

Search:

```text
SSL
TLS
X509
certificate
verify
TrustManager
hostname
```

Native applications may implement certificate validation themselves.

Investigate:

```text
Where validation occurs
Which certificate is expected
What happens when validation fails
Whether the result is enforced
```

Potential findings may include:

```text
Improper certificate validation
Missing hostname validation
Weak trust configuration
Security-sensitive traffic not properly protected
```

---

# 37. Cryptographic Analysis

Search for:

```text
AES
RSA
SHA
MD5
HMAC
encrypt
decrypt
Cipher
EVP
key
IV
nonce
```

Investigate:

```text
Algorithm
Key generation
Key storage
Key length
IV/nonce handling
Mode of operation
Randomness
Key hardcoding
Sensitive plaintext
```

Do not report:

```text
"MD5 string found"
```

as a vulnerability automatically.

Determine how the algorithm is actually used.

---

# 38. Hardcoded Secrets

Search:

```text
api_key
apikey
secret
client_secret
password
token
private_key
access_token
```

Then:

```text
String
 ↓
XREF
 ↓
Function
 ↓
Usage
 ↓
Sensitivity
 ↓
Impact
```

A hardcoded value becomes more interesting when it is:

```text
Sensitive
Reusable
Privileged
Valid
Accessible to an attacker
```

---

# 39. Authentication Logic

Search for:

```text
login
authenticate
password
token
session
credential
verify
```

Trace:

```text
Input
 ↓
Validation
 ↓
Native function
 ↓
Comparison
 ↓
Return value
 ↓
Application behavior
```

Questions:

```text
Is authentication performed locally?
Is the server also validating it?
Are credentials exposed?
Is a secret embedded in the binary?
Can client-side state influence authentication?
```

---

# 40. Authorization Logic

Search for:

```text
admin
role
permission
privilege
isAdmin
access
authorize
```

Follow:

```text
Input
 ↓
Role calculation
 ↓
Security decision
 ↓
Native function
 ↓
Return value
```

Then validate against the server.

A client-side check is not automatically exploitable if the server independently enforces authorization.

---

# 41. API Endpoint Analysis

Search native binaries for:

```text
https://
http://
/api/
graphql
oauth
login
auth
```

You may find:

```text
https://api.example.test
```

Then trace the reference.

Determine:

```text
Who uses it?
Which function calls it?
What data is transmitted?
Is authentication required?
Is a secret embedded?
```

---

# 42. Control-Flow Analysis

Control flow represents how execution moves through a function.

Example:

```text
             Start
               │
               ▼
           Check Value
            /       \
          True      False
           │          │
           ▼          ▼
        Success     Failure
```

Ghidra can visualize this.

This is particularly useful for:

```text
Authentication
Authorization
Security checks
Error handling
Validation
Cryptographic decisions
```

---

# 43. Ghidra Graphs

The graph view can help visualize:

```text
Basic Blocks
Branches
Loops
Calls
Conditional paths
```

Use graphs when a function becomes difficult to understand from linear assembly.

Conceptually:

```text
       ┌─────────┐
       │  START  │
       └────┬────┘
            │
       ┌────▼────┐
       │  CHECK  │
       └─┬─────┬─┘
         │     │
       YES     NO
         │     │
    ┌────▼┐ ┌──▼────┐
    │PASS │ │ FAIL  │
    └─────┘ └───────┘
```

---

# 44. Data Flow Analysis

Data-flow analysis asks:

```text
Where did this value come from?
Where does it go?
What modifies it?
Which function receives it?
```

Example:

```text
User Input
    ↓
Native Function
    ↓
Transformation
    ↓
Encryption
    ↓
Network
```

This can help identify:

```text
Sensitive data exposure
Weak validation
Hardcoded keys
Unsafe processing
Unexpected data flows
```

---

# 45. Function Call Trees

Instead of analyzing one function in isolation:

```text
main()
  ↓
authenticate()
  ↓
validateToken()
  ↓
decrypt()
  ↓
checkExpiry()
```

You can follow the complete call chain.

This is especially useful when a function has a generic name such as:

```text
FUN_0010A220
```

The caller and callee relationships can reveal its actual purpose.

---

# 46. Structures and Data Types

Native programs often use structures.

Conceptually:

```c
struct User {
    char *username;
    int role;
    char *token;
};
```

Correct data types can make decompiled code dramatically easier to understand.

Instead of:

```c
*(int *)(param_1 + 0x10)
```

you may eventually understand it as:

```c
user->role
```

Learning to identify:

```text
Pointers
Structures
Arrays
Strings
Integers
Function pointers
```

is important for advanced Ghidra work.

---

# 47. Memory Analysis Concepts

You should understand:

```text
Stack
Heap
Registers
Pointers
Addresses
Offsets
Memory regions
```

Typical conceptual layout:

```text
High Address
┌─────────────┐
│    Stack    │
├─────────────┤
│             │
│    Heap     │
│             │
├─────────────┤
│   .bss      │
├─────────────┤
│   .data     │
├─────────────┤
│   .rodata   │
├─────────────┤
│   .text     │
└─────────────┘
Low Address
```

Understanding memory makes native reverse engineering much easier.

---

# 48. Ghidra and ADB

ADB helps correlate static findings with the Android runtime.

Useful commands:

```bash
adb devices
```

Get installed packages:

```bash
adb shell pm list packages
```

Inspect package path:

```bash
adb shell pm path <package>
```

Example:

```bash
adb shell pm path com.example.app
```

Pull an APK:

```bash
adb pull <apk-path>
```

Monitor logs:

```bash
adb logcat
```

This creates a useful workflow:

```text
ADB
 ↓
Obtain APK
 ↓
Extract .so
 ↓
Ghidra
 ↓
Static Analysis
 ↓
ADB / Runtime
 ↓
Validate
```

---

# 49. Ghidra and Frida

Ghidra and Frida complement each other.

## Ghidra

Answers:

```text
Where is the function?
What does the function appear to do?
What calls it?
What values does it use?
```

## Frida

Answers:

```text
Does the function execute?
What arguments are passed?
What values are returned?
What happens at runtime?
```

Combined workflow:

```text
Ghidra
   ↓
Identify function
   ↓
Understand parameters
   ↓
Understand return value
   ↓
Frida
   ↓
Observe runtime behavior
   ↓
Confirm hypothesis
```

---

# 50. Static → Dynamic Workflow

A professional workflow:

```text
                    APK
                     │
                     ▼
              Extract .so
                     │
                     ▼
                  Ghidra
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       Strings     XREF     Functions
          │          │          │
          └──────────┼──────────┘
                     ▼
                Decompiler
                     │
                     ▼
                Assembly
                     │
                     ▼
              Security Logic
                     │
                     ▼
                   Frida
                     │
                     ▼
              Runtime Behavior
                     │
                     ▼
                  Burp/ADB
                     │
                     ▼
             Security Impact
```

---

# 51. Native Binary Patching

Ghidra can be used to understand and, in controlled environments, modify native binaries.

Examples of legitimate use:

```text
Training binaries
CTF challenges
Your own applications
Authorized security research
```

Typical concept:

```text
Original .so
     ↓
Ghidra
     ↓
Find instruction
     ↓
Understand behavior
     ↓
Controlled modification
     ↓
Export
     ↓
Replace test binary
     ↓
Rebuild application
     ↓
Sign
     ↓
Test
```

---

# 52. Ghidra Patch Workflow

A controlled lab workflow:

```text
APK
 │
 ▼
Extract .so
 │
 ▼
Open in Ghidra
 │
 ▼
Find security function
 │
 ▼
Analyze control flow
 │
 ▼
Understand conditional branch
 │
 ▼
Make controlled lab modification
 │
 ▼
Export modified binary
 │
 ▼
Replace .so in test APK
 │
 ▼
Rebuild APK
 │
 ▼
Sign APK
 │
 ▼
Install
 │
 ▼
Observe behavior
```

The purpose is to understand whether a security decision exists only on the client and whether it actually protects something important.

---

# 53. Common Ghidra Analysis Mistakes

## Mistake 1 — Trusting the decompiler completely

Decompiler output is reconstructed.

Always verify important logic with:

```text
Assembly
XREF
Callers
Runtime behavior
```

---

## Mistake 2 — Assuming a string is a vulnerability

Finding:

```text
password
```

doesn't prove a password vulnerability.

Trace its use.

---

## Mistake 3 — Reporting root bypass as a vulnerability

Bypassing root detection is generally:

```text
Testing technique
```

Determine whether the application relies on the control for a meaningful security boundary.

---

## Mistake 4 — Ignoring callers

A function may appear vulnerable in isolation but be safely used.

Always inspect:

```text
Caller
Callee
Input
Output
Security decision
```

---

## Mistake 5 — Ignoring server-side validation

Mobile applications are untrusted clients.

For authentication and authorization:

```text
Client
  ↓
Server
```

The server should enforce security-sensitive decisions.

---

## Mistake 6 — Only using static analysis

Static analysis gives you a hypothesis.

Dynamic analysis can validate it.

Best practice:

```text
Static
 +
Dynamic
 +
Server-side validation
```

---

# 54. Testing Technique vs Finding

This distinction is critical.

## Technique

A technique is a method used to investigate a target.

Examples:

```text
Ghidra
XREF analysis
Decompiler
ARM64 analysis
String analysis
Control-flow analysis
Frida
Binary patching
```

## Finding

A finding is a confirmed security weakness.

Examples:

```text
Hardcoded privileged credential
Weak cryptographic implementation
Improper certificate validation
Client-side authorization weakness
Sensitive information exposure
Native memory corruption
Insecure authentication logic
```

### Example

```text
Ghidra
  ↓
Find root detection
  ↓
Understand root check
  ↓
Modify test application
  ↓
Root check bypassed
```

This does **not automatically mean**:

```text
"Root detection bypass vulnerability"
```

You must determine the security impact.

---

# 55. Ghidra Technique → Finding Mapping

| Ghidra Technique      | Potential Finding                        |
| --------------------- | ---------------------------------------- |
| String analysis       | Hardcoded secret                         |
| XREF analysis         | Sensitive security logic                 |
| Decompiler analysis   | Weak authentication logic                |
| ARM64 analysis        | Native implementation weakness           |
| JNI analysis          | Native security weakness                 |
| Crypto analysis       | Weak cryptography                        |
| Root-check analysis   | Improper client-side security dependency |
| Certificate analysis  | Improper certificate validation          |
| Control-flow analysis | Authorization weakness                   |
| Data-flow analysis    | Sensitive data exposure                  |
| Function analysis     | Security-control weakness                |
| Native API analysis   | Unsafe native implementation             |
| Binary patching       | Client-side enforcement validation       |

The word **potential** is important.

A technique identifies something worth investigating.

The actual finding requires validation and demonstrated impact.

---

# 56. Complete Beginner Practice Lab

## 🎯 Objective

Learn Ghidra by analyzing an intentionally vulnerable Android application or native Android training binary.

Recommended targets:

```text
DIVA
InjuredAndroid
AndroidGoat
MobileHackingLab Android challenges
Android crackmes
Your own Android application
```

Do not use a production application for this beginner exercise.

---

## Lab Architecture

```text
Training APK
     │
     ▼
Extract APK
     │
     ▼
Find .so
     │
     ▼
Identify ARM64
     │
     ▼
Ghidra
     │
     ├── Strings
     ├── Functions
     ├── XREF
     ├── Decompiler
     └── Assembly
            │
            ▼
       Understand Logic
            │
            ▼
        Android Runtime
            │
            ▼
       Validate Behavior
```

---

## Step 1 — Obtain the Training APK

Place your authorized training APK in:

```text
ghidra-lab/
└── vulnerable.apk
```

---

## Step 2 — Extract the APK

```bash
unzip vulnerable.apk -d extracted/
```

Find native libraries:

```bash
find extracted -name "*.so"
```

Windows:

```cmd
dir /s /b extracted\*.so
```

---

## Step 3 — Select an ARM64 Library

Example:

```text
extracted/
└── lib/
    └── arm64-v8a/
        └── libnative.so
```

Copy it:

```text
ghidra-lab/
└── libnative.so
```

---

## Step 4 — Identify the Binary

```bash
file libnative.so
```

Then:

```bash
readelf -h libnative.so
```

Record:

```text
Architecture:
Class:
Machine:
```

---

## Step 5 — Create Ghidra Project

Open Ghidra.

Create:

```text
Android-Ghidra-Lab
```

Import:

```text
libnative.so
```

---

## Step 6 — Run Auto Analysis

Select:

```text
Yes
```

Allow Ghidra to analyze the binary.

---

## Step 7 — Find Strings

Open:

```text
Window
 ↓
Defined Strings
```

Search for:

```text
password
secret
root
debug
token
admin
certificate
```

Choose one interesting string.

---

## Step 8 — Follow XREF

Right-click the string and locate its references.

Follow:

```text
String
 ↓
XREF
 ↓
Function
```

Write down the function name.

Example:

```text
FUN_00102340
```

---

## Step 9 — Open the Decompiler

Open the function in the decompiler.

Try to answer:

```text
What parameters does it receive?

What functions does it call?

What values does it compare?

What does it return?

What happens when the condition is true?

What happens when it is false?
```

---

## Step 10 — Inspect Assembly

Look at the corresponding instructions.

Identify:

```text
CMP
B.EQ
B.NE
CBZ
CBNZ
MOV
BL
RET
```

Try to map:

```text
C-like code
       ↓
Assembly
```

---

## Step 11 — Rename the Function

If you understand its purpose:

```text
FUN_00102340
```

rename it to something meaningful, such as:

```text
checkValue
```

Do not rename it until you have enough evidence.

---

## Step 12 — Find the Caller

Use XREF on the function.

Find:

```text
Who calls this function?
```

Then inspect the caller.

This is extremely important.

A function's purpose often becomes clear from its caller.

---

## Step 13 — Trace the Result

Suppose:

```c
result = checkValue(input);

if (result) {
    allowAction();
}
else {
    denyAction();
}
```

Trace:

```text
checkValue()
     ↓
return value
     ↓
caller
     ↓
security decision
```

This is where Ghidra becomes useful for security testing.

---

## Step 14 — Compare With Runtime Behavior

Install the training application:

```bash
adb install vulnerable.apk
```

Launch:

```bash
adb shell monkey -p <package> 1
```

Observe:

```bash
adb logcat
```

Compare:

```text
Static Analysis
      ↓
Predicted Behavior
      ↓
Runtime Behavior
```

---

## Step 15 — Document Your Analysis

Create:

```text
analysis.md
```

Use:

```markdown
# Ghidra Analysis

## Target

Application:

## Library

Library:

## Architecture

ARM64 / x86_64 / etc.

## Interesting Function

Name:

Address:

## What I Found

## Static Analysis

## Decompiler Analysis

## Assembly Analysis

## XREF

## Runtime Validation

## Security Impact

## Conclusion
```

---

# 57. Intermediate Practice

Once the beginner lab is complete, choose a binary containing:

```text
JNI
Root detection
Anti-debugging
Crypto
Certificate validation
```

For each target:

```text
1. Find the string
2. Follow XREF
3. Identify function
4. Read decompiler
5. Read assembly
6. Identify caller
7. Trace return value
8. Observe runtime behavior
9. Document conclusion
```

---

# 58. Advanced Practice

For advanced practice, study:

```text
ARM64 calling conventions
ELF internals
PLT/GOT
Dynamic linking
JNI RegisterNatives
Function pointers
Structures
Virtual tables
C++ name mangling
C++ RTTI
Compiler optimizations
Control-flow flattening
Obfuscation
Anti-debugging
Native cryptography
Memory corruption
Ghidra scripting
```

---

# 59. Ghidra Cheat Sheet

## Project

```text
Create Project
Import File
Open Program
```

## Analysis

```text
Auto Analyze
Functions
Symbols
Strings
XREF
Decompiler
Listing
```

## Important concepts

```text
Function
Address
Label
Symbol
XREF
Basic Block
Control Flow
Data Flow
Register
Stack
Heap
Pointer
Structure
```

## Android

```text
APK
DEX
JNI
ELF
.so
ARM64
```

## Useful external commands

```bash
file libnative.so
readelf -h libnative.so
readelf -S libnative.so
readelf -s libnative.so
strings libnative.so
```

---

# 60. Mobile Reverse Engineering Workflow

The complete Ghidra-centered workflow:

```text
                  Android APK
                       │
                       ▼
                Extract APK
                       │
                       ▼
                  Find .so
                       │
                       ▼
                Identify ABI
                       │
                       ▼
                   Ghidra
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Strings   Functions   Symbols
             │         │         │
             └─────────┼─────────┘
                       ▼
                     XREF
                       │
                       ▼
                  Decompiler
                       │
                       ▼
                   Assembly
                       │
                       ▼
                 ARM64 / JNI
                       │
                       ▼
              Security Logic
                       │
                       ▼
               Runtime Testing
                       │
                 ┌─────┴─────┐
                 ▼           ▼
               Frida        ADB
                 │           │
                 └─────┬─────┘
                       ▼
                Validate Impact
                       │
                       ▼
                    Report
```

---

# 61. Learning Roadmap

## 🟢 Level 1 — Ghidra Basics

Learn:

```text
Projects
Import
Auto Analysis
CodeBrowser
Listing
Symbol Tree
Functions
Strings
XREF
Decompiler
```

---

## 🟡 Level 2 — Native Android

Learn:

```text
APK structure
.so files
ELF
ABI
JNI
ARM64
Native functions
```

---

## 🟠 Level 3 — Security Analysis

Learn:

```text
Root detection
Anti-debugging
Certificate validation
Cryptography
Authentication
Authorization
Hardcoded secrets
API logic
```

---

## 🔴 Level 4 — Advanced Reverse Engineering

Learn:

```text
Assembly
Calling conventions
Memory
Pointers
Structures
Function pointers
C++
ELF internals
Dynamic linking
Obfuscation
Anti-analysis
Binary patching
Ghidra scripting
```

---

# 62. Final Checklist

## Ghidra Fundamentals

```text
[ ] Install Ghidra
[ ] Create project
[ ] Import binary
[ ] Identify architecture
[ ] Run Auto Analysis
[ ] Navigate Listing
[ ] Understand Symbol Tree
[ ] Find functions
[ ] Rename functions
[ ] Search strings
[ ] Follow XREF
[ ] Read decompiler
[ ] Read assembly
```

## Android Native

```text
[ ] Extract APK
[ ] Find .so
[ ] Identify ABI
[ ] Understand ELF
[ ] Identify JNI
[ ] Find JNI_OnLoad
[ ] Find RegisterNatives
[ ] Understand ARM64
```

## Security

```text
[ ] Analyze root detection
[ ] Analyze anti-debugging
[ ] Analyze certificate validation
[ ] Analyze crypto
[ ] Search secrets
[ ] Analyze authentication
[ ] Analyze authorization
[ ] Trace sensitive data
```

## Dynamic Validation

```text
[ ] ADB
[ ] logcat
[ ] Frida
[ ] Runtime validation
[ ] Server-side validation
```

## Reporting

```text
[ ] Evidence
[ ] Reproduction
[ ] Impact
[ ] Severity
[ ] CWE
[ ] OWASP mapping
[ ] Remediation
```

---

# 63. Responsible Use

Ghidra is a powerful reverse-engineering tool.

Use it only for:

```text
✓ Your own applications
✓ Authorized penetration tests
✓ Company-approved security assessments
✓ CTFs
✓ Crackmes
✓ Vulnerable training applications
✓ Research binaries
✓ Isolated security labs
```

Do not use reverse engineering, binary modification, or security-control bypass techniques against systems without authorization.

For professional engagements, maintain:

```text
Written authorization
Defined scope
Rules of engagement
Approved targets
Testing window
Evidence handling
Responsible disclosure
```

---

# 🎯 Final Takeaway

Ghidra should not be treated simply as a tool that converts:

```text
.so → C code
```

A mobile penetration tester should use it to build an understanding of the complete execution path:

```text
String
  ↓
XREF
  ↓
Function
  ↓
Decompiler
  ↓
Assembly
  ↓
Caller
  ↓
Data Flow
  ↓
Security Decision
  ↓
Runtime Behavior
  ↓
Security Impact
```

The most important Ghidra skill is therefore **not memorizing every ARM64 instruction**.

It is learning to answer:

> **Where is the security-sensitive logic, how does it work, who calls it, what data reaches it, what does it return, and does that behavior create a real security impact?**

That is how Ghidra becomes a practical tool for **Android reverse engineering and mobile application penetration testing**.
