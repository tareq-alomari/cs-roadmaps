# 02 — أنظمة التشغيل: Linux & Windows

---

## Linux — الركيزة الأساسية

معظم أدوات الأمن تعمل على Linux. Kali Linux و Parrot OS هما التوزيعتان الأكثر استخدامًا.

### مهارات Linux الأساسية للأمن

```bash
# إدارة الملفات والصلاحيات
chmod, chown, umask
ls -la              # عرض الصلاحيات
find / -perm -4000  # البحث عن SUID files (مهم جدًا)

# إدارة العمليات
ps aux              # عرض جميع العمليات
top / htop
kill, pkill
lsof -i             # الملفات والمنافذ المفتوحة

# الشبكة
ifconfig / ip a
netstat -tulpn / ss -tulpn
iptables            # Firewall rules
curl, wget

# المستخدمون والصلاحيات
/etc/passwd         # ملف المستخدمين
/etc/shadow         # كلمات المرور المشفرة
/etc/sudoers        # صلاحيات sudo
whoami, id, groups

# السجلات (Logs) — مهمة جدًا
/var/log/auth.log   # محاولات تسجيل الدخول
/var/log/syslog     # سجل النظام
journalctl -f       # متابعة السجلات لحظيًا

# Bash Scripting للأمن
#!/bin/bash
for ip in $(seq 1 254); do
    ping -c 1 192.168.1.$ip &>/dev/null && echo "192.168.1.$ip is up"
done
```

### Linux Privilege Escalation (مهم جدًا)

```
المسار النموذجي:
Shell محدود → User عادي → Root

تقنيات شائعة:
- SUID/SGID binaries
- Sudo misconfigurations
- Cron jobs
- Writable /etc/passwd
- Kernel exploits
- PATH hijacking
```

---

## Windows Internals

### مفاهيم أساسية

- **Registry** — `HKLM`, `HKCU` وكيف يُستغل للـ Persistence
- **Active Directory** — الأهم في بيئات الشركات
- **Windows Services** — كيف تُستغل للـ Privilege Escalation
- **Token & Privileges** — Impersonation, SeImpersonatePrivilege
- **SAM Database** — تخزين كلمات المرور
- **NTLM vs Kerberos** — بروتوكولات المصادقة

### أوامر Windows الأساسية للأمن

```cmd
# استطلاع النظام
whoami /all          # المستخدم والصلاحيات
net users            # قائمة المستخدمين
net localgroup administrators
systeminfo           # معلومات النظام
tasklist             # العمليات الجارية
netstat -ano         # الاتصالات النشطة

# Active Directory
net user /domain
net group /domain
nltest /domain_trusts
```

### PowerShell للأمن

```powershell
# فحص الصلاحيات
Get-LocalUser
Get-LocalGroupMember Administrators

# البحث عن ملفات حساسة
Get-ChildItem -Recurse -Filter "*.config" | Select-String "password"

# تنفيذ أوامر عن بُعد (للاختبار المصرح)
Invoke-Command -ComputerName TARGET -ScriptBlock { whoami }
```

---

## توزيعات الأمن

| التوزيعة | الاستخدام |
|----------|-----------|
| **Kali Linux** | اختبار الاختراق الشامل |
| **Parrot OS** | أخف من Kali، مناسب للأجهزة المتوسطة |
| **REMnux** | تحليل البرمجيات الخبيثة |
| **SIFT Workstation** | التحقيق الجنائي الرقمي |
| **Tails** | الخصوصية والأمن الشخصي |

---

## ⏱️ الوقت المقدر: 4-6 أسابيع

## مصادر
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) — تعلم Linux تفاعليًا
- [Linux Privilege Escalation - TCM Security](https://academy.tcm-sec.com/)
- كتاب: *The Linux Command Line* - William Shotts (مجاني)
