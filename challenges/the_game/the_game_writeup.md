# TryHackMe – The Game

> 🚫 This write-up follows TryHackMe’s guidelines. No flags, credentials, or direct answers are shared.

_A walkthrough of the [The Game](https://tryhackme.com/room/hfb1thegame) challenge._

---

## 📚 Table of Contents
- [🧠 Overview](#-overview)
- [📡 Target Information](#-target-information)
- [🛰️ Reconnaissance](#-reconnaissance)
- [🛠️ Exploitation](#-exploitation)
- [🧨 Privilege Escalation](#-privilege-escalation)
- [🧠 Lessons Learned](#-lessons-learned)
- [📚 Resources](#-resources)

---

## 🧠 Overview
- **Category:** Reverse Engineering
- **Difficulty:** Easy
- **Date Completed:** 2025-06-29
- **Tools Used:** `strings`, `grep`, `wine`, `file`

---

## 📡 Target Information

| Detail  | Value        |
|---------|--------------|
| Binary  | `Tetrix.exe` |

---

## 🛰️ Reconnaissance

### 🔍 File Identification

```bash
file Tetrix.exe
```

Output:
```
Tetrix.exe: PE32+ executable (GUI) x86-64, for MS Windows
```

The binary is a Windows-based 64-bit executable, likely written in C/C++ and possibly stripped.

---

### 🔎 Strings Analysis

```bash
strings Tetrix.exe | grep THM{
```

This quickly revealed a potential flag-like string embedded directly in the binary. This indicates the flag is not dynamically generated or encoded — just statically stored.

---

### 🚫 Skipped Network/Directory Recon

As this was a standalone reverse engineering challenge with no server or web component, tools like `gobuster` or `nmap` were not necessary.

---

## 🛠️ Exploitation

Rather than exploiting a live service, this challenge was solved by statically analyzing the binary.

### Steps Taken:

1. **Ran `strings`** on the binary to pull out all ASCII-readable content.
2. **Used `grep`** to search for known flag format (`THM{`).
3. **Found the flag** directly in the output — no reversing or patching needed.

```bash
strings Tetrix.exe | grep THM{
```

This implies the flag was likely embedded for a quick-win beginner-level intro to binary analysis.

---

## 🧨 Privilege Escalation

Not applicable — this was a single-user binary analysis challenge, no privilege escalation was involved.

---

## 🧠 Lessons Learned

- Always try `strings` + `grep` first — it can be the fastest path to the flag.
- Understanding file types and OS platforms (e.g., PE files vs. ELF) helps choose the right tools.
- Even "reverse engineering" challenges can sometimes be solved without reverse engineering 😄
- CTFs often hide flags in obvious places to build confidence before escalating difficulty.

---

## 📚 Resources

- [`strings` command](https://man7.org/linux/man-pages/man1/strings.1.html)
- [TryHackMe](https://tryhackme.com)
