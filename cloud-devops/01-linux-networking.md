# 01 — Linux & Networking للـ DevOps

---

## Linux الأساسي للـ DevOps

```bash
# إدارة الخدمات
systemctl start nginx
systemctl enable nginx
systemctl status nginx
journalctl -u nginx -f

# إدارة الموارد
top / htop / btop
df -h              # مساحة القرص
free -h            # الذاكرة
iostat             # I/O القرص
vmstat             # إحصائيات النظام

# Networking
ss -tulpn          # المنافذ المفتوحة
ip addr / ip route
curl -I https://example.com
dig example.com
traceroute example.com

# Performance
strace -p PID      # System calls
lsof -p PID        # الملفات المفتوحة
tcpdump -i eth0    # الحزم الشبكية

# Cron Jobs
crontab -e
# دقيقة ساعة يوم شهر يوم_الأسبوع
0 2 * * * /backup.sh
```

---

## Bash Scripting للـ DevOps

```bash
#!/bin/bash
set -euo pipefail    # أوقف عند أول خطأ

# متغيرات
APP_NAME="myapp"
ENV=${1:-"staging"}  # Default value

# Arrays
SERVICES=("api" "worker" "scheduler")
for service in "${SERVICES[@]}"; do
    echo "Deploying $service..."
    kubectl rollout restart deployment/$service
done

# Functions
check_health() {
    local url=$1
    if curl -sf "$url/health" > /dev/null; then
        echo "✓ $url is healthy"
    else
        echo "✗ $url is down" && exit 1
    fi
}

# Logging
log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*"; }
log "Deployment started"
```

---

## Networking للـ DevOps

```bash
# DNS
nslookup service.internal
dig @10.0.0.53 service.internal

# Load Testing
ab -n 1000 -c 50 http://api.example.com/    # Apache Benchmark
wrk -t4 -c100 -d30s http://api.example.com  # أفضل

# SSL/TLS
openssl s_client -connect example.com:443
openssl x509 -in cert.pem -text -noout     # فحص الشهادة
certbot renew                               # تجديد Let's Encrypt

# Port Forwarding
ssh -L 5432:db.internal:5432 bastion        # Local forwarding
ssh -R 8080:localhost:3000 server           # Remote forwarding
```

---

## ⏱️ الوقت المقدر: 2 أشهر

## مصادر
- [Linux Upskill Challenge](https://linuxupskillchallenge.org/) ← مجاني 20 يوم
- [The Linux Command Line](https://linuxcommand.org/tlcl.php) ← مجاني
