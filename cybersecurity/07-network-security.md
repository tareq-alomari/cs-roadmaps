# 07 — أمن الشبكات (Network Security)

---

## مفاهيم الدفاع

### Firewall

```
أنواع الـ Firewall:
1. Packet Filtering    — يفحص الـ Header فقط (IP, Port)
2. Stateful Inspection — يتتبع حالة الاتصال
3. Application Layer   — يفهم البروتوكول (NGFW)
4. WAF                 — مخصص لتطبيقات الويب

قواعد iptables:
# رفض كل شيء ثم السماح بما هو مطلوب
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# السماح لـ SSH فقط
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# السماح للـ Established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

### IDS / IPS

```
IDS (Intrusion Detection System)  — يراقب ويُنبه فقط
IPS (Intrusion Prevention System) — يراقب ويمنع

Snort — أشهر IDS/IPS مفتوح المصدر
# قاعدة Snort بسيطة
alert tcp any any -> 192.168.1.0/24 80 (msg:"HTTP Traffic"; sid:1000001;)

Suricata — بديل حديث وأسرع
Zeek (Bro) — تحليل الشبكة بعمق
```

### VPN & Zero Trust

```
VPN Types:
- Site-to-Site VPN    ← ربط مكاتب
- Remote Access VPN   ← موظفون من المنزل
- Split Tunneling     ← جزء من الترافيك فقط عبر VPN

Zero Trust Model:
"لا تثق بأحد، تحقق من الجميع"
- مصادقة مستمرة
- Least Privilege Access
- Micro-segmentation
```

---

## هجمات الشبكة

### Man-in-the-Middle (MITM)

```bash
# ARP Poisoning
arpspoof -i eth0 -t VICTIM_IP GATEWAY_IP
arpspoof -i eth0 -t GATEWAY_IP VICTIM_IP

# SSL Stripping
sslstrip -l 8080

# مع MITMf Framework
mitmf --arp --spoof --gateway 192.168.1.1 --target 192.168.1.5 -i eth0

# Bettercap (الأحدث)
bettercap -iface eth0
# في Bettercap:
arp.spoof on
net.sniff on
```

### Network Scanning & Sniffing

```bash
# Passive Sniffing
tcpdump -i eth0 -w capture.pcap
tcpdump -i eth0 'port 80' -A    # HTTP بدون تشفير

# Wireshark filters
http.request.method == "POST"
tcp.port == 443
ip.addr == 192.168.1.10
dns.qry.name contains "password"

# Network Discovery
netdiscover -r 192.168.1.0/24
arp-scan -l
```

### Wireless Security

```bash
# Wi-Fi Hacking (WPA2)
# الخطوات:
airmon-ng start wlan0                    # Monitor mode
airodump-ng wlan0mon                     # فحص الشبكات
airodump-ng -c 6 --bssid MAC -w cap wlan0mon  # التقاط الـ Handshake
aireplay-ng -0 1 -a BSSID -c CLIENT wlan0mon  # Deauth لإعادة الاتصال
aircrack-ng -w rockyou.txt cap.cap       # كسر كلمة المرور

# Evil Twin Attack
hostapd-wpe evil_twin.conf               # نقطة وصول وهمية

# Wi-Fi Attacks أخرى
- PMKID Attack (أسرع من Handshake)
- WPS PIN Brute Force (reaver)
- Rogue AP
```

### DoS / DDoS

```bash
# للاختبار المعملي فقط!
# hping3
hping3 -S --flood -V -p 80 TARGET      # SYN Flood

# Slowloris (HTTP DoS)
slowloris TARGET -p 80 -s 500

# مقاومة DDoS:
- Rate Limiting
- CDN (Cloudflare)
- Anycast Routing
- Traffic Scrubbing
```

---

## Network Monitoring & Defense

```bash
# Security Information and Event Management (SIEM)
Splunk          ← الأكثر انتشارًا في الشركات
ELK Stack       ← مجاني (Elasticsearch + Logstash + Kibana)
Wazuh           ← مفتوح المصدر، ممتاز

# Network Traffic Analysis
ntopng          ← مراقبة الشبكة لحظيًا
Security Onion  ← توزيعة كاملة للرصد والتحليل

# Honeypots
cowrie          ← SSH/Telnet Honeypot
dionaea         ← Malware Honeypot
T-Pot           ← منصة Honeypot متكاملة
```

---

## Network Segmentation

```
الشبكة الجيدة:
[Internet] → [DMZ] → [Firewall] → [Internal Network]
                                        ↓
                               [VLAN 10: Servers]
                               [VLAN 20: Users]
                               [VLAN 30: IoT]

قاعدة Least Privilege:
كل VLAN يتحدث فقط مع ما يحتاجه
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Security Onion Documentation](https://docs.securityonion.net/)
- كتاب: *Network Security Assessment* - Chris McNab
- [Practical Packet Analysis](https://nostarch.com/packetanalysis3) - Chris Sanders
