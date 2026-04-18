# 01 — أساسيات الشبكات

---

## نموذج OSI

```
7 Layers:
┌────────────────────────────────────────┐
7 │ Application    ← HTTP, DNS, FTP     │
6 │ Presentation    ← SSL, JPEG, UTF-8   │
5 │ Session         ← RPC, NetBIOS       │
──────────────────────────────────────────┤
4 │ Transport       ← TCP, UDP           │
3 │ Network         ← IP, ICMP, routers  │
2 │ Data Link      ← MAC, switches      │
1 │ Physical       ← cables, hubs       │
└────────────────────────────────────────┘

remember: All People Seem To Need Data Processing
```

---

## IP Addressing

### IPv4

```
Classes:
A: 1.0.0.0    - 126.255.255.255   (0-127)
B: 128.0.0.0 - 191.255.255.255   (128-191)
C: 192.0.0.0 - 223.255.255.255   (192-223)
D: 224.0.0.0 - 239.255.255.255   (multicast)
E: 240.0.0.0 - 255.255.255.255   (reserved)

Private Ranges:
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16

Special:
0.0.0.0       ← all addresses
127.0.0.1     ← loopback
255.255.255.255 ← broadcast
```

### Subnetting

```
Subnet Mask:
/24 = 255.255.255.0     = 256 hosts
/25 = 255.255.255.128   = 128 hosts
/26 = 255.255.255.192   = 64 hosts
/27 = 255.255.255.224   = 32 hosts
/28 = 255.255.255.240   = 16 hosts
/29 = 255.255.255.248   = 8 hosts
/30 = 255.255.255.252   = 4 hosts

Formula:
Hosts = 2^(32 - prefix) - 2
```

### CIDR vs VLSM

```
CIDR (Classless Inter-Domain Routing):
192.168.1.0/24    ← /24 = 256 addresses

VLSM (Variable Length Subnet Masking):
192.168.1.0/26    ← 64 addresses (HQ)
192.168.1.64/26   ← 64 addresses (Branch 1)
192.168.1.128/26  ← 64 addresses (Branch 2)
192.168.1.192/26  ← 64 addresses (Future)
```

---

## TCP vs UDP

```
TCP (Reliable):
- Connection-oriented
- 3-way handshake: SYN → SYN-ACK → ACK
- Flow control & Congestion control
- Ordered delivery
- Use: HTTP, HTTPS, FTP, SSH, Email

UDP (Fast):
- Connectionless
- No handshake
- No reliability
- Use: DNS, DHCP, VoIP, Video streaming, Gaming

Ports Well-Known:
20    FTP Data
21    FTP Control
22    SSH
23    Telnet
25    SMTP
53    DNS
67/68 DHCP
80    HTTP
443   HTTPS
3306  MySQL
5432  PostgreSQL
6379  Redis
```

---

## DNS

```
DNS Query Types:

Recursive:
Client → DNS Server → Root → TLD → Authoritative

Iterative:
Client → DNS Server → Root (Not found)
Client → DNS Server → TLD (Not found)
Client → DNS Server → Authoritative (Found!)

Record Types:
A      ← IPv4 address
AAAA   ← IPv6 address
CNAME  ← Alias
MX     ← Mail server
NS     ← Name server
PTR    ← Reverse DNS
TXT    ← SPF, DKIM
```

---

## DHCP

```
DHCP Process (DORA):

Discover    ← Client broadcasts "any DHCP server?"
Offer      ← Server offers IP address
Request    ← Client requests the offered IP
Ack        ← Server acknowledges

DHCP Scope Example:
Pool: 192.168.1.100 - 192.168.1.200
Subnet: 255.255.255.0
Router: 192.168.1.1
DNS: 8.8.8.8, 1.1.1.1
Lease: 8 hours
```

---

## ARP

```
ARP Process:

1. Host wants to send to 192.168.1.10
2. Checks ARP table for MAC of 192.168.1.10
3. If not found, broadcasts ARP Request:
   "Who has 192.168.1.10? Tell 192.168.1.5"

4. Target replies with ARP Reply:
   "192.168.1.10 is at aa:bb:cc:dd:ee:ff"

5. Host adds to ARP table and sends frame

Commands:
show arp         ← Cisco
arp -a           ← Windows
ip neigh show    ← Linux
```

---

## NAT

```
NAT Types:

Static NAT    ← One-to-one mapping
Router(config)# ip nat inside source static 192.168.1.10 203.0.113.10

Dynamic NAT   ← Pool-based
Router(config)# ip nat pool MYPOOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
Router(config)# ip nat inside source list 1 pool MYPOOL

PAT (Port Address Translation) ← Overloading:
Router(config)# ip nat inside source list 1 interface fastethernet0/0 overload

Inside vs Outside:
interface g0/0
  ip nat inside
interface g0/1
  ip nat outside
```

---

## Ethernet

```
Ethernet Frame:
┌──────────┬─────────────┬──────────┬─────────┬────────────┐
│ Preamble │ Destination │  Source  │  Type   │   Data    │
│  7 bytes │   6 bytes  │ 6 bytes  │ 2 bytes │ 46-1500B │
└──────────┴─────────────┴──────────┴─────────┴────────────┘
│                      ↑
│              EtherType:
              0x0800 = IPv4
              0x0806 = ARP
              0x86DD = IPv6

MAC Address:
00:1A:2B:3C:4D:5E
← Vendor ID ← Unique ID
```

---

## Network Devices

| Device | Layer | Function |
|--------|-------|----------|
| Hub | L1 | Repeat signal, collision domain |
| Bridge | L2 | Filter by MAC |
| Switch | L2 | Multiport bridge |
| Router | L3 | Route between networks |
| Layer 3 Switch | L3 | Router + Switch |
| Firewall | L3+ | Security, filtering |
| WAP | L2 | Wireless access |
| Load Balancer | L3+ | Distribute traffic |

---

## Troubleshooting Commands

```bash
# Windows
ipconfig /all           ← Full IP config
ipconfig /release       ← Release DHCP
ipconfig /renew         ← Renew DHCP
tracert 8.8.8.8         ← Trace route
nslookup google.com     ← DNS lookup
netstat -an             ← Connections
arp -a                  ← ARP table

# Linux
ip addr show             ← IP config
ip route show           ← Routing table
traceroute 8.8.8.8      ← Trace route
dig google.com          ← DNS lookup
netstat -tulpn          ← Listening ports
ip neigh show           ← ARP table
```

---

## ⏱️ الوقت المقدر: 1-2 أشهر

## مصادر
- [Jeremy's IT Lab - CCNA](https://www.youtube.com/playlist?list=PLxbwE86jOdgM1MWMQ0FBQTEVMxNBqLLqN) ← مجاني وشامل
- [Packet Tracer](https://www.netacad.com/courses/packet-tracer) ← للتدريب العملي
- [SubnettingPractice](https://subnettingpractice.com)