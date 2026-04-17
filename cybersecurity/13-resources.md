# 13 — المصادر والمراجع

---

## منصات التدريب العملي

### مجانية

| المنصة | الوصف |
|--------|-------|
| [TryHackMe](https://tryhackme.com) | أفضل للمبتدئين، مسارات منظمة |
| [HackTheBox](https://hackthebox.com) | للمتوسط والمتقدم |
| [VulnHub](https://vulnhub.com) | VMs للتنزيل والتدريب المحلي |
| [PortSwigger Academy](https://portswigger.net/web-security) | أمن الويب فقط — الأفضل في نوعه |
| [PentesterLab](https://pentesterlab.com) | تمارين الويب والـ Code Review |
| [OverTheWire](https://overthewire.org) | Wargames لتعلم Linux والأمن |
| [PicoCTF](https://picoctf.org) | CTF للمبتدئين من Carnegie Mellon |
| [DFIR.training](https://www.dfir.training/) | تمارين التحقيق الجنائي |
| [BlueTeamLabs](https://blueteamlabs.online/) | تمارين الدفاع والتحليل |

### مدفوعة (تستحق الاستثمار)

| المنصة | التخصص |
|--------|---------|
| [TCM Security Academy](https://academy.tcm-sec.com) | Pentesting شامل |
| [SANS Institute](https://www.sans.org) | الأعلى مستوى، مكلف جدًا |
| [INE Security](https://ine.com) | eJPT, eCPPT, eWPT |
| [Offensive Security](https://www.offensive-security.com) | OSCP, OSEP, OSWE |

---

## الكتب المرجعية

### للمبتدئين

- *CompTIA Security+ Study Guide* - Mike Chapple
- *The Linux Command Line* - William Shotts (مجاني)
- *Networking All-in-One For Dummies* - Doug Lowe

### اختبار الاختراق

- **Penetration Testing** - Georgia Weidman ← أفضل بداية
- **The Web Application Hacker's Handbook** - Stuttard & Pinto
- **Hacking: The Art of Exploitation** - Jon Erickson ← للـ Low-level
- **Black Hat Python** - Justin Seitz
- **Red Team Development and Operations** - Joe Vest

### الدفاع والتحليل

- **The Art of Memory Forensics** - Ligh, Case, Levy, Walters ← مرجع الـ Memory Forensics
- **Practical Malware Analysis** - Sikorski & Honig ← الكتاب الأساسي
- **Applied Network Security Monitoring** - Chris Sanders
- **Threat Hunting with Elastic Stack** - Andrew Pease

### التشفير

- **Serious Cryptography** - Jean-Philippe Aumasson
- **The Code Book** - Simon Singh (للفهم التاريخي)
- **Applied Cryptography** - Bruce Schneier

---

## القنوات والمدونات

### YouTube

| القناة | المحتوى |
|--------|---------|
| [NetworkChuck](https://youtube.com/@NetworkChuck) | Networking & Hacking للمبتدئين |
| [John Hammond](https://youtube.com/@_JohnHammond) | CTF, Malware Analysis |
| [IppSec](https://youtube.com/@ippsec) | HackTheBox Walkthroughs |
| [TCM Security](https://youtube.com/@TCMSecurityAcademy) | Pentesting courses |
| [LiveOverflow](https://youtube.com/@LiveOverflow) | Binary Exploitation, CTF |
| [STÖK](https://youtube.com/@STOKfredrik) | Bug Bounty |
| [13Cubed](https://youtube.com/@13Cubed) | DFIR |

### المدونات والمواقع

- [HackTricks](https://book.hacktricks.xyz) ← مرجع شامل للـ Pentesting
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [GTFOBins](https://gtfobins.github.io) ← Linux PrivEsc
- [LOLBAS](https://lolbas-project.github.io) ← Windows PrivES
- [ired.team](https://www.ired.team) ← Red Teaming Notes
- [0xdf hacks stuff](https://0xdf.gitlab.io) ← HackTheBox Writeups

---

## المجتمعات

```
Reddit:
- r/netsec              ← أخبار الأمن
- r/hacking             ← نقاشات عامة
- r/AskNetSec           ← أسئلة للمحترفين
- r/oscp                ← خاص بـ OSCP

Discord:
- TryHackMe Discord
- HackTheBox Discord
- TCM Security Discord
- SANS Community

Twitter/X:
- @GossiTheDog          ← Threat Intel
- @SwiftOnSecurity       ← Security awareness
- @malwareunicorn        ← Malware Analysis
- @hacks4pancakes        ← Security professional
```

---

## الممارسة اليومية

```
خطة يومية مقترحة (1-2 ساعة):

السبت - الثلاثاء:
- TryHackMe room واحدة على الأقل
- أو HackTheBox machine

الأربعاء:
- قراءة في مدونة أو كتاب

الخميس:
- CTF إن وجد (CTFtime.org)

الجمعة:
- مراجعة ما تعلمت هذا الأسبوع
- كتابة notes أو Writeup
```

---

## خارطة تعلم CTF

```
CTF = Capture The Flag — مسابقات الأمن

الفئات:
├── Web        ← SQL Injection, XSS, SSRF, IDOR
├── Pwn        ← Buffer Overflow, ROP chains
├── Rev        ← Reverse Engineering, Crackmes
├── Crypto     ← Cryptography challenges
├── Forensics  ← Memory, Disk, Network analysis
├── OSINT      ← جمع المعلومات المفتوحة
└── Misc       ← متنوع

مواقع CTF:
- CTFtime.org  ← تقويم المسابقات
- pwn.college  ← Binary Exploitation تدريجي
- Crackmes.one ← Reverse Engineering
```

---

## الجانب القانوني والأخلاقي

```
⚠️ مهم جدًا:

✓ اختبر فقط ما تملك إذنًا صريحًا لاختباره
✓ Bug Bounty Programs = إذن رسمي
✓ بيئاتك المعزولة = آمنة
✓ احصل على اتفاقية كتابية قبل أي Pentest

برامج Bug Bounty للبدء:
- HackerOne (hackerone.com)
- Bugcrowd (bugcrowd.com)
- Intigriti (intigriti.com)

قوانين تجنبها:
- Computer Fraud and Abuse Act (CFAA) - أمريكا
- Computer Misuse Act - بريطانيا
- قوانين الجرائم الإلكترونية في بلدك
```
