# 04 — التشفير وعلم الكريبتوغرافي

---

## لماذا التشفير مهم في الأمن؟

- فهم كيف تُحمى البيانات (وكيف تُكسر)
- تحليل بروتوكولات الاتصال الآمنة
- كشف الثغرات في تطبيقات التشفير

---

## المفاهيم الأساسية

### 1. التشفير المتماثل (Symmetric)

```
نفس المفتاح للتشفير وفك التشفير

الخوارزميات:
- AES (128/256 bit)  ← المعيار الحالي
- DES / 3DES         ← قديم وضعيف
- RC4                ← مكسور، تجنبه

الأوضاع (Modes):
- ECB  ← ضعيف (نفس البلوك = نفس المخرج)
- CBC  ← شائع لكن عرضة لـ Padding Oracle
- GCM  ← الأفضل (يوفر Authentication أيضًا)
- CTR  ← جيد للـ Streaming
```

### 2. التشفير غير المتماثل (Asymmetric)

```
مفتاح عام للتشفير + مفتاح خاص لفك التشفير

الخوارزميات:
- RSA (1024/2048/4096 bit)
- ECC (Elliptic Curve)    ← أكثر كفاءة
- Diffie-Hellman          ← لتبادل المفاتيح

الاستخدامات:
- TLS/HTTPS
- SSH
- Email Signing (PGP)
- Digital Certificates
```

### 3. دوال التجزئة (Hash Functions)

```
مدخل من أي حجم → مخرج ثابت الحجم

MD5    → 128 bit  ← مكسور، لا تستخدمه للأمان
SHA-1  → 160 bit  ← مكسور جزئيًا
SHA-256 → 256 bit ← آمن
SHA-3  → متغير   ← الأحدث والأكثر أمانًا
bcrypt / argon2   ← للكلمات السر تحديدًا

الخصائص المطلوبة:
- One-way (لا يمكن العكس)
- Collision resistant (لا يوجد مدخلان بنفس المخرج)
- Avalanche effect (تغيير bit واحد يغير كل المخرج)
```

### 4. بروتوكولات الأمان

```
TLS 1.3 (Transport Layer Security)
الخطوات:
1. Client Hello (الإصدارات والـ Ciphers المدعومة)
2. Server Hello (اختيار الـ Cipher)
3. Certificate Exchange
4. Key Exchange (ECDHE)
5. Finished (بدء التشفير)

SSH
- Public Key Authentication
- Host Key Verification
- Port Forwarding

PGP/GPG
- تشفير الإيميلات والملفات
- Digital Signatures
```

---

## الثغرات الشائعة في التشفير

```
1. Padding Oracle Attack
   - استغلال رسائل خطأ الـ Padding في CBC
   - أداة: padbuster

2. Length Extension Attack
   - يطال MD5 و SHA-1 و SHA-256
   - لا يطال HMAC أو SHA-3

3. Weak Random Number Generation
   - استخدام rand() بدلاً من /dev/urandom
   - Python: os.urandom() لا random.random()

4. ECB Mode Vulnerability
   - نفس البلوك = نفس التشفير → يكشف الأنماط

5. Hardcoded Keys
   - مفاتيح ثابتة في الكود المصدري
```

---

## تطبيق عملي

```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.primitives import hashes, hmac
import os

# AES-GCM (الطريقة الصحيحة)
key = os.urandom(32)    # 256-bit key
nonce = os.urandom(12)  # 96-bit nonce

cipher = Cipher(algorithms.AES(key), modes.GCM(nonce))
encryptor = cipher.encryptor()
ciphertext = encryptor.update(b"Secret message") + encryptor.finalize()
tag = encryptor.tag

# HMAC للتوقيع
h = hmac.HMAC(key, hashes.SHA256())
h.update(b"Message to sign")
signature = h.finalize()
```

---

## ⏱️ الوقت المقدر: 3-4 أسابيع

## مصادر
- [Cryptopals Challenges](https://cryptopals.com/) ← الأفضل عمليًا
- [Dan Boneh's Cryptography Course (Coursera)](https://www.coursera.org/learn/crypto)
- كتاب: *Serious Cryptography* - Jean-Philippe Aumasson
