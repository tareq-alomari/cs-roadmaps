# 09 — التحقيق الجنائي الرقمي (Digital Forensics & Incident Response)

---

## DFIR = Digital Forensics + Incident Response

---

## مبادئ التحقيق الجنائي

```
قواعد أساسية:
1. لا تعبث بالدليل الأصلي — اعمل على نسخة
2. احتفظ بسلسلة الحفاظ على الأدلة (Chain of Custody)
3. وثّق كل خطوة تقوم بها
4. Order of Volatility:
   الأكثر تطايرًا (يضيع أولًا) → الأقل تطايرًا
   RAM → Network connections → Running processes
   → Disk → Logs → External media
```

---

## 1. الطب الشرعي للذاكرة (Memory Forensics)

```bash
# أخذ dump للذاكرة
# Windows
winpmem.exe memory.dmp
# Linux
sudo dd if=/dev/mem of=memory.dmp bs=1M

# التحليل بـ Volatility
pip install volatility3

# معلومات النظام
vol -f memory.dmp windows.info

# العمليات
vol -f memory.dmp windows.pslist       # قائمة العمليات
vol -f memory.dmp windows.pstree       # كشجرة
vol -f memory.dmp windows.psscan       # يكشف العمليات المخفية

# الشبكة
vol -f memory.dmp windows.netstat

# الـ Malware Hunting
vol -f memory.dmp windows.malfind       # كود مشبوه محقون في العمليات
vol -f memory.dmp windows.cmdline       # الأوامر التي نُفذت

# استخراج ملف
vol -f memory.dmp windows.dumpfiles --pid 1234
```

---

## 2. الطب الشرعي للقرص (Disk Forensics)

```bash
# أخذ Image للقرص
dd if=/dev/sda of=disk.img bs=4M status=progress
# أو
dcfldd if=/dev/sda of=disk.img hash=md5 hashlog=hash.txt

# التحقق من النزاهة
md5sum disk.img

# التحليل بـ Autopsy (واجهة رسومية)
autopsy                    # فتح الواجهة
# يدعم: File recovery, Timeline, Keyword search, Web artifacts

# FTK Imager (Windows)
# - أخذ الصور
# - فحص الـ File System

# الملفات المحذوفة
photorec disk.img          # استرداد الملفات المحذوفة
foremost -i disk.img       # File carving
scalpel disk.img           # File carving متقدم

# الـ File System Timeline
mactime -b bodyfile.txt -d > timeline.csv

# تحليل الـ Metadata
exiftool suspicious_file.pdf
exiftool -r images/
```

---

## 3. الطب الشرعي لنظام Windows

```
Artifacts المهمة:

المسجل (Registry):
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run   ← Autorun
NTUSER.DAT → MRU (Most Recently Used)
ShellBags → Folders accessed
UserAssist → Programs run

ملفات الـ Prefetch:
C:\Windows\Prefetch\*.pf
- تسجل آخر مرة نُفذ فيها البرنامج
- أداة: PECmd.exe

Event Logs:
C:\Windows\System32\winevt\Logs\
- Security.evtx  ← تسجيل الدخول (4624, 4625, 4648)
- System.evtx    ← أحداث النظام
- Application.evtx

أداة تحليل الـ Logs: EvtxECmd.exe, Chainsaw

Browser Artifacts:
- History, Cookies, Cache, Downloads
- أداة: BrowsingHistoryView, Hindsight

Jump Lists, LNK Files, Shellbags
```

---

## 4. الاستجابة للحوادث (Incident Response)

### دورة الاستجابة للحوادث

```
1. Preparation   ← قبل وقوع الحادثة
2. Identification ← اكتشاف الحادثة
3. Containment   ← احتواء الضرر
4. Eradication   ← إزالة السبب
5. Recovery      ← استعادة الخدمة
6. Lessons Learned ← ماذا تعلمنا؟
```

### Live Response (على نظام مصاب)

```powershell
# Windows - جمع الأدلة لحظيًا

# العمليات
Get-Process | Select-Object Name, Id, Path, StartTime

# الاتصالات الشبكية
Get-NetTCPConnection | Where-Object State -eq Established

# Autorun
Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run

# المستخدمون المتصلون
query session

# الملفات المعدلة مؤخرًا
Get-ChildItem C:\ -Recurse | Where-Object {$_.LastWriteTime -gt (Get-Date).AddDays(-1)}

# أداة كاملة
Kape (Kroll Artifact Parser and Extractor)
```

---

## 5. Network Forensics

```bash
# تحليل pcap
wireshark capture.pcap
tcpdump -r capture.pcap -n

# استخراج الملفات من pcap
tcpflow -r capture.pcap
networkMiner capture.pcap      ← واجهة رسومية ممتازة
tshark -r capture.pcap -q -z io,stat,0

# تحليل DNS
zeek -r capture.pcap
cat dns.log | awk '{print $10}' | sort | uniq -c | sort -rn
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- كتاب: *The Art of Memory Forensics* - Ligh, Case, Levy, Walters
- [SANS FOR508](https://www.sans.org/cyber-security-courses/advanced-incident-response-threat-hunting-training/)
- [Volatility Documentation](https://volatility3.readthedocs.io/)
- [Autopsy User Guide](https://sleuthkit.org/autopsy/)
- [BlueTeamLabs Online](https://blueteamlabs.online/) ← تمارين عملية مجانية
