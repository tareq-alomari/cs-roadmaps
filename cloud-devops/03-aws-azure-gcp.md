# 03 — AWS / Azure / GCP

---

## AWS — الأساسيات

```bash
# الخدمات الأكثر استخدامًا
EC2          ← Virtual Machines
S3           ← Object Storage
RDS          ← Managed Databases
Lambda       ← Serverless Functions
ECS/EKS      ← Containers
VPC          ← Networking
IAM          ← Identity & Access
CloudWatch   ← Monitoring
Route53      ← DNS
CloudFront   ← CDN

# CLI أساسي
aws ec2 describe-instances --region us-east-1
aws s3 cp file.txt s3://my-bucket/
aws logs tail /aws/lambda/my-function --follow
```

### Serverless

```python
# Lambda Function
import json

def handler(event, context):
    body = json.loads(event['body'])
    return {
        'statusCode': 200,
        'body': json.dumps({'message': f"Hello {body['name']}"})
    }
```

```yaml
# Serverless Framework
service: my-api
provider:
  name: aws
  runtime: python3.11

functions:
  hello:
    handler: handler.handler
    events:
      - httpApi:
          path: /hello
          method: post
```

---

## Infrastructure as Code — Terraform

```hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  tags = {
    Name = "web-server"
  }
}

resource "aws_s3_bucket" "data" {
  bucket = "my-app-data-bucket"
}

output "public_ip" {
  value = aws_instance.web.public_ip
}
```

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [AWS Free Tier](https://aws.amazon.com/free/) ← ابدأ مجانًا
- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [A Cloud Guru](https://acloudguru.com/) ← مدفوع، ممتاز للشهادات
