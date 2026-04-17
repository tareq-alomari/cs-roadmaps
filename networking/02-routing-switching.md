# 02 — Routing & Switching

---

## Routing

### Static vs Dynamic Routing

```
Static:
ip route 192.168.2.0 255.255.255.0 192.168.1.1
→ يدوي، للشبكات الصغيرة أو المسارات الثابتة

Dynamic Routing Protocols:
┌─────────────────────────────────────────┐
│ IGP (داخل نفس الـ AS)                  │
│   Distance Vector: RIP (قديم، EIGRP)   │
│   Link State: OSPF ← الأكثر استخدامًا │
│                                         │
│ EGP (بين الـ AS)                        │
│   BGP ← يدير الإنترنت كله              │
└─────────────────────────────────────────┘
```

### OSPF

```
cisco# conf t
cisco(config)# router ospf 1
cisco(config-router)# network 192.168.1.0 0.0.0.255 area 0
cisco(config-router)# network 10.0.0.0 0.0.0.3 area 0

المفاهيم:
- Areas (تقسيم الشبكة لتقليل الـ LSA flooding)
- Area 0 = Backbone Area (إلزامي)
- DR/BDR election في Broadcast networks
- SPF Algorithm (Dijkstra)
```

### BGP (أساسي)

```
BGP = Border Gateway Protocol
- يربط الـ ISPs والشركات الكبرى
- Path vector protocol
- يستخدم TCP port 179

مفاهيم:
- AS Number (ASN)
- iBGP vs eBGP
- BGP Attributes: AS-PATH, MED, LOCAL_PREF
- BGP Communities
```

---

## Switching

### VLANs

```
cisco# conf t
cisco(config)# vlan 10
cisco(config-vlan)# name Sales
cisco(config)# interface fastethernet 0/1
cisco(config-if)# switchport mode access
cisco(config-if)# switchport access vlan 10

# Trunk port (يحمل كل الـ VLANs)
cisco(config-if)# switchport mode trunk
cisco(config-if)# switchport trunk allowed vlan 10,20,30
```

### STP (Spanning Tree Protocol)

```
المشكلة: Loops في الشبكة → Broadcast Storm
الحل: STP يحسب الـ Spanning Tree ويوقف المنافذ الزائدة

الأنواع:
- STP (802.1D)    ← قديم، بطيء (30-50 ثانية)
- RSTP (802.1w)   ← أسرع (1-2 ثانية)
- MSTP (802.1s)   ← لكل VLAN شجرة

Port States: Blocking → Listening → Learning → Forwarding
```

### Inter-VLAN Routing

```
طريقة 1: Router-on-a-stick
interface g0/0.10
  encapsulation dot1q 10
  ip address 192.168.10.1 255.255.255.0

طريقة 2: Layer 3 Switch (أفضل)
ip routing
interface vlan 10
  ip address 192.168.10.1 255.255.255.0
```

---

## Network Troubleshooting

```bash
# خطوات منهجية (من الطبقة 1 إلى 7)
# L1: هل الكابل متصل؟
# L2: show mac address-table
# L3: ping, traceroute, show ip route
# L4: telnet IP PORT, netstat
# L7: curl, browser

# Cisco Commands
show ip interface brief    # حالة جميع الواجهات
show ip route              # جدول الـ Routing
show running-config        # الإعدادات الحالية
show interfaces            # إحصائيات تفصيلية
debug ip ospf events       # debugging
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) ← مجاني للتدريب
- [Jeremy's IT Lab (YouTube)](https://www.youtube.com/@JeremysITLab) ← CCNA مجاني
- كتاب: *Computer Networking: A Top-Down Approach* - Kurose & Ross
