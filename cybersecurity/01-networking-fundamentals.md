# 01 — أساسيات الشبكات

## لماذا الشبكات مهمة في الأمن؟

الأمن السيبراني في جوهره هو فهم كيف تتواصل الأنظمة — ثم إيجاد نقاط الضعف في هذا التواصل.

---

## المواضيع الأساسية

### نموذج OSI

| الطبقة | الاسم | البروتوكولات |
|--------|-------|--------------|
| 7 | Application | HTTP, FTP, DNS, SMTP |
| 6 | Presentation | SSL/TLS, JPEG |
| 5 | Session | NetBIOS, RPC |
| 4 | Transport | TCP, UDP |
| 3 | Network | IP, ICMP, ARP |
| 2 | Data Link | Ethernet, MAC |
| 1 | Physical | Cables, Wi-Fi |

### البروتوكولات الحرجة للأمن

- **TCP/IP** — فهم الـ Handshake (SYN, SYN-ACK, ACK) وكيف يُستغل
- **DNS** — DNS Spoofing, DNS Tunneling, DNS Enumeration
- **HTTP/HTTPS** — Headers, Methods, Status Codes, TLS Handshake
- **ARP** — ARP Poisoning / Man-in-the-Middle
- **DHCP** — DHCP Starvation, Rogue DHCP
- **ICMP** — Ping Sweeps, ICMP Tunneling
- **SMB** — EternalBlue, Pass-the-Hash
- **SMTP/IMAP** — Email Spoofing, Phishing infrastructure

### Subnetting

```
192.168.1.0/24  →  256 عنوان (254 قابل للاستخدام)
10.0.0.0/8      →  شبكة كبيرة جدًا (Class A)
CIDR Notation   →  أساسي لفهم الـ Scanning
```

### الأدوات العملية

```bash
# تحليل الشبكة
wireshark          # تحليل الحزم بواجهة رسومية
tcpdump            # تحليل الحزم من الـ CLI
nmap               # مسح الشبكات والمنافذ
netstat / ss       # عرض الاتصالات النشطة
arp -a             # جدول ARP
traceroute         # تتبع مسار الحزم
dig / nslookup     # استعلامات DNS
```

---

## مفاهيم متقدمة

- **VPN & Tunneling** — كيف تعمل، وكيف تُكشف
- **Proxy & Reverse Proxy** — MITM attacks
- **Load Balancers** — وكيف تؤثر على اختبار الاختراق
- **IPv6** — مفاهيم الأمن الخاصة بها
- **Wireless Security** — WPA2/WPA3, Evil Twin, Deauth attacks

---

## تمارين عملية

1. قم بالتقاط حزم HTTP باستخدام Wireshark وافهم كل حقل
2. افحص شبكتك المنزلية بـ `nmap -sV 192.168.1.0/24`
3. تتبع رحلة DNS request من المتصفح حتى الـ Server
4. نفذ هجوم ARP Poisoning في بيئة معزولة (VM)

---

## ⏱️ الوقت المقدر: 3-4 أسابيع

## مصادر
- [Cisco Networking Academy](https://www.netacad.com/)
- كتاب: *Computer Networks* - Andrew Tanenbaum
- [Wireshark Documentation](https://www.wireshark.org/docs/)
