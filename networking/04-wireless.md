# 04 — Wireless Networking

---

## معايير Wi-Fi

| المعيار | الاسم التسويقي | التردد | السرعة القصوى |
|---------|---------------|--------|---------------|
| 802.11n | Wi-Fi 4 | 2.4/5 GHz | 600 Mbps |
| 802.11ac | Wi-Fi 5 | 5 GHz | 3.5 Gbps |
| 802.11ax | Wi-Fi 6/6E | 2.4/5/6 GHz | 9.6 Gbps |
| 802.11be | Wi-Fi 7 | 2.4/5/6 GHz | 46 Gbps |

---

## أمان الشبكات اللاسلكية

```
WEP    ← مكسور كليًا، لا تستخدمه
WPA    ← ضعيف
WPA2 Personal (PSK)  ← مقبول للمنازل
WPA2 Enterprise      ← للشركات (802.1X + RADIUS)
WPA3   ← الأحدث، الأكثر أمانًا

802.1X Authentication:
Supplicant (Client) → Authenticator (AP) → RADIUS Server
```

---

## Enterprise Wireless

```
Controller-based:
- Cisco WLC (Wireless LAN Controller)
- يدير مئات الـ Access Points مركزيًا

Cloud-managed:
- Cisco Meraki
- Aruba Central
- Ubiquiti UniFi ← الأشهر في SMB

Concepts:
- SSID Profiles
- RF Channels & Power
- Roaming (802.11r Fast BSS Transition)
- Band Steering
- Quality of Service (QoS)
```

---

## Site Survey

```
قبل تركيب الشبكة اللاسلكية:
1. Passive Survey  ← استماع فقط
2. Active Survey   ← الاتصال والاختبار
3. Predictive Survey ← محاكاة قبل التركيب

أدوات:
- Ekahau Site Survey ← الأحترف
- WiFi Analyzer (Android) ← مجاني
- inSSIDer
```

---

## ⏱️ الوقت المقدر: 4-6 أسابيع

## مصادر
- [CWNA Study Guide](https://www.cwnp.com/certifications/cwna)
- [Wireless LAN Professionals](https://www.wlanpros.com/)
