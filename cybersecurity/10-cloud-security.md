# 10 — أمن الحوسبة السحابية (Cloud Security)

---

## المزودون الرئيسيون

- **AWS** (Amazon Web Services) — الأكثر انتشارًا
- **Azure** (Microsoft) — الأقوى في بيئات الشركات
- **GCP** (Google Cloud) — الأفضل في AI/ML

---

## نموذج المسؤولية المشتركة

```
Cloud Provider يؤمن:
- Physical infrastructure
- Hypervisor
- Managed services

أنت تؤمن:
- Data
- Applications
- Identity & Access Management (IAM)
- Network configuration
- Operating System (في IaaS)
```

---

## AWS Security

### IAM (Identity & Access Management)

```json
// مبدأ Least Privilege — منح أقل صلاحية ممكنة
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}

// الأخطاء الشائعة:
- Action: "*"  ← خطر!
- Resource: "*" ← خطر!
- مشاركة Access Keys في الكود
```

### الأدوات والأوامر

```bash
# AWS CLI للـ Pentesting (بصلاحيات مخولة)
aws sts get-caller-identity           # من أنا؟
aws iam list-users                    # المستخدمون
aws iam list-roles                    # الأدوار
aws s3 ls                             # الـ Buckets
aws ec2 describe-instances            # الأجهزة الافتراضية
aws lambda list-functions             # Lambda functions

# فحص الـ S3 Buckets العامة
aws s3 ls s3://bucket-name --no-sign-request   # بدون credentials

# ScoutSuite — فحص شامل لإعدادات AWS
python scout.py aws

# Prowler — أداة CSPM
prowler aws

# Pacu — AWS Exploitation Framework
pacu
```

### الثغرات الشائعة في AWS

```
1. S3 Bucket Misconfiguration
   - Bucket عام يحتوي على بيانات حساسة
   - اختبار: aws s3 ls s3://bucket --no-sign-request

2. Instance Metadata Service (IMDS)
   - SSRF → http://169.254.169.254/latest/meta-data/
   - استخراج IAM credentials
   - الحل: IMDSv2 إلزامي

3. Over-privileged IAM Roles
   - Role للـ EC2 بصلاحيات Admin

4. Exposed Access Keys
   - في GitHub, في الكود, في Environment variables

5. Security Groups مفتوحة
   - 0.0.0.0/0 على منافذ حساسة (22, 3389, 5432)
```

---

## Container Security (Docker & Kubernetes)

### Docker Security

```bash
# فحص الـ Image
trivy image nginx:latest              # فحص الثغرات
docker scout cves nginx:latest        # بديل حديث
dive nginx:latest                     # فحص الطبقات

# أخطاء شائعة
docker run --privileged              # خطر! وصول كامل للـ Host
docker run -v /:/host                # تثبيت الـ Root filesystem
docker run -u root                   # تشغيل كـ Root

# Escape من الـ Container
# إذا كنت Root داخل Container --privileged:
mount /dev/sda1 /mnt
chroot /mnt
```

### Kubernetes Security

```yaml
# Security Context الصحيح
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
  readOnlyRootFilesystem: true
  allowPrivilegeEscalation: false

# Network Policies
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

```bash
# Kubernetes Pentesting
kubectl auth can-i --list            # ما الذي أستطيع فعله؟
kube-hunter                          # فحص الثغرات
kubeaudit all                        # فحص الإعدادات
```

---

## Cloud Security Best Practices

```
1. Identity
   ✓ MFA على جميع الحسابات
   ✓ Least Privilege لكل Role
   ✓ Rotate Access Keys دوريًا
   ✓ لا تستخدم Root Account للعمل اليومي

2. Network
   ✓ VPC مع Subnets خاصة وعامة
   ✓ Security Groups محكمة
   ✓ WAF أمام تطبيقات الويب
   ✓ VPN أو Direct Connect للوصول الداخلي

3. Data
   ✓ تشفير البيانات في Transit (TLS)
   ✓ تشفير البيانات في Rest (KMS)
   ✓ S3 Bucket versioning + MFA Delete

4. Monitoring
   ✓ CloudTrail مفعّل
   ✓ GuardDuty للكشف عن التهديدات
   ✓ Config Rules لمراقبة الامتثال
   ✓ Security Hub لتجميع التنبيهات
```

---

## ⏱️ الوقت المقدر: 2-4 أشهر

## مصادر
- [CloudGoat](https://github.com/RhinoSecurityLabs/cloudgoat) ← بيئة AWS ضعيفة للتدريب
- [Flaws.cloud](http://flaws.cloud/) ← تمارين AWS Security مجانية
- [AWS Security Documentation](https://docs.aws.amazon.com/security/)
- كورس: [AWS Security Specialty (SCS-C02)](https://aws.amazon.com/certification/certified-security-specialty/)
- كتاب: *Hacking the Cloud* - HackTricks
