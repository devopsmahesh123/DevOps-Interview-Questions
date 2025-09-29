## ☁️ AWS DevOps Scenario-Based Questions

---

### **Question 1: How do you create an EC2 instance with a specific AMI and security group using Terraform?**

**Answer:**
1. Define provider and resource:
   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   resource "aws_instance" "web" {
     ami           = "ami-0abcdef1234567890"
     instance_type = "t2.micro"
     security_groups = ["web-sg"]
     tags = {
       Name = "WebServer"
     }
   }
   ```

2. Initialize and apply:
   ```bash
   terraform init
   terraform apply
   ```

**Tips & Best Practices:**
- Use variables for AMI and instance type.
- Tag resources for cost tracking and automation.

**Example:**
```bash
terraform plan -var="ami_id=ami-0abc123"
```

---

### **Question 2: How do you automate S3 bucket creation with versioning and encryption using AWS CLI?**

**Answer:**
1. Create bucket:
   ```bash
   aws s3api create-bucket --bucket my-secure-bucket --region us-east-1
   ```

2. Enable versioning:
   ```bash
   aws s3api put-bucket-versioning --bucket my-secure-bucket --versioning-configuration Status=Enabled
   ```

3. Enable encryption:
   ```bash
   aws s3api put-bucket-encryption --bucket my-secure-bucket --server-side-encryption-configuration '{
     "Rules": [{
       "ApplyServerSideEncryptionByDefault": {
         "SSEAlgorithm": "AES256"
       }
     }]
   }'
   ```

**Tips & Best Practices:**
- Use bucket policies to restrict access.
- Enable logging for audit trails.

**Example:**
```bash
aws s3 ls s3://my-secure-bucket --recursive
```

---

### **Question 3: How do you set up a CI/CD pipeline using CodePipeline and CodeBuild for a GitHub repo?**

**Answer:**
1. Create CodeBuild project:
   - Connect GitHub.
   - Define `buildspec.yml`.

2. Create CodePipeline:
   - Source: GitHub
   - Build: CodeBuild
   - Deploy: (e.g., ECS, Lambda)

**Tips & Best Practices:**
- Use webhooks for GitHub triggers.
- Store secrets in AWS Secrets Manager.

**Example:**
```yaml
# buildspec.yml
version: 0.2
phases:
  build:
    commands:
      - echo "Building app"
artifacts:
  files:
    - '**/*'
```

---

### **Question 4: How do you restrict SSH access to EC2 using Security Groups?**

**Answer:**
1. Edit security group:
   ```bash
   aws ec2 authorize-security-group-ingress \
     --group-id sg-12345678 \
     --protocol tcp \
     --port 22 \
     --cidr 203.0.113.0/24
   ```

2. Remove open access:
   ```bash
   aws ec2 revoke-security-group-ingress \
     --group-id sg-12345678 \
     --protocol tcp \
     --port 22 \
     --cidr 0.0.0.0/0
   ```

**Tips & Best Practices:**
- Use bastion hosts for secure access.
- Rotate key pairs regularly.

**Example:**
```bash
ssh -i mykey.pem ec2-user@203.0.113.10
```

---

### **Question 5: How do you create an IAM policy to allow read-only access to S3?**

**Answer:**
1. Define policy:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Effect": "Allow",
       "Action": ["s3:GetObject", "s3:ListBucket"],
       "Resource": ["arn:aws:s3:::mybucket", "arn:aws:s3:::mybucket/*"]
     }]
   }
   ```

2. Attach to IAM user or role.

**Tips & Best Practices:**
- Use least privilege principle.
- Review policies with IAM Access Analyzer.

**Example:**
```bash
aws iam put-user-policy --user-name devuser --policy-name S3ReadOnly --policy-document file://s3-read.json
```

---

### **Question 6: How do you automate EBS volume snapshot creation daily using AWS CLI and cron?**

**Answer:**
1. Create snapshot:
   ```bash
   aws ec2 create-snapshot --volume-id vol-12345678 --description "Daily backup"
   ```

2. Add to cron:
   ```bash
   crontab -e
   # Daily at 2 AM
   0 2 * * * /usr/local/bin/ebs_snapshot.sh
   ```

**Tips & Best Practices:**
- Tag snapshots for cleanup.
- Use Lambda for lifecycle automation.

**Example:**
```bash
aws ec2 describe-snapshots --filters Name=volume-id,Values=vol-12345678
```

---

### **Question 7: How do you deploy a containerized app to AWS ECS using Terraform?**

**Answer:**
1. Define ECS resources:
   ```hcl
   resource "aws_ecs_cluster" "main" {
     name = "my-cluster"
   }

   resource "aws_ecs_task_definition" "app" {
     family                = "my-app"
     container_definitions = jsonencode([{
       name      = "web"
       image     = "nginx"
       cpu       = 256
       memory    = 512
       essential = true
     }])
   }
   ```

2. Create service and ALB.

**Tips & Best Practices:**
- Use Fargate for serverless containers.
- Monitor with CloudWatch.

**Example:**
```bash
terraform apply -auto-approve
```

---

### **Question 8: How do you configure CloudWatch alarms for EC2 CPU usage?**

**Answer:**
1. Create alarm:
   ```bash
   aws cloudwatch put-metric-alarm \
     --alarm-name "HighCPU" \
     --metric-name CPUUtilization \
     --namespace AWS/EC2 \
     --statistic Average \
     --period 300 \
     --threshold 80 \
     --comparison-operator GreaterThanThreshold \
     --dimensions Name=InstanceId,Value=i-12345678 \
     --evaluation-periods 2 \
     --alarm-actions arn:aws:sns:us-east-1:123456789012:NotifyMe
   ```

**Tips & Best Practices:**
- Use SNS for notifications.
- Combine with Auto Scaling policies.

**Example:**
```bash
aws sns subscribe --topic-arn arn:aws:sns:us-east-1:123456789012:NotifyMe --protocol email --notification-endpoint mahesh@example.com
```

---

### **Question 9: How do you use AWS CLI to list all public IPs of running EC2 instances?**

**Answer:**
```bash
aws ec2 describe-instances \
  --filters Name=instance-state-name,Values=running \
  --query 'Reservations[*].Instances[*].PublicIpAddress' \
  --output text
```

**Tips & Best Practices:**
- Use `jq` for advanced filtering.
- Avoid exposing public IPs unnecessarily.

**Example:**
```bash
aws ec2 describe-instances | jq '.Reservations[].Instances[].PublicIpAddress'
```

---

### **Question 10: How do you automate infrastructure provisioning using Terraform with remote state in S3?**

**Answer:**
1. Configure backend:
   ```hcl
   terraform {
     backend "s3" {
       bucket = "my-terraform-state"
       key    = "infra/terraform.tfstate"
       region = "us-east-1"
     }
   }
   ```

2. Initialize:
   ```bash
   terraform init
   ```

**Tips & Best Practices:**
- Enable versioning on S3 bucket.
- Use DynamoDB for state locking.

**Example:**
```bash
terraform plan && terraform apply
```

---

### **Question 11: How do you create a custom VPC with public and private subnets using Terraform?**

**Answer:**
1. Define VPC and subnets:
   ```hcl
   resource "aws_vpc" "main" {
     cidr_block = "10.0.0.0/16"
   }

   resource "aws_subnet" "public" {
     vpc_id     = aws_vpc.main.id
     cidr_block = "10.0.1.0/24"
     map_public_ip_on_launch = true
   }

   resource "aws_subnet" "private" {
     vpc_id     = aws_vpc.main.id
     cidr_block = "10.0.2.0/24"
   }
   ```

2. Add route tables and IGW.

**Tips & Best Practices:**
- Use `terraform modules` for reusable VPC patterns.
- Tag subnets for auto-discovery (e.g., by EKS).

**Example:**
```bash
terraform apply -var-file="vpc.tfvars"
```

---

### **Question 12: How do you deploy a Lambda function using AWS CLI with a ZIP package?**

**Answer:**
1. Create ZIP:
   ```bash
   zip function.zip index.js
   ```

2. Deploy:
   ```bash
   aws lambda create-function \
     --function-name MyFunction \
     --runtime nodejs18.x \
     --role arn:aws:iam::123456789012:role/lambda-role \
     --handler index.handler \
     --zip-file fileb://function.zip
   ```

**Tips & Best Practices:**
- Use layers for shared dependencies.
- Monitor with CloudWatch Logs.

**Example:**
```bash
aws lambda invoke --function-name MyFunction output.json
```

---

### **Question 13: How do you provision an RDS MySQL instance using Terraform?**

**Answer:**
```hcl
resource "aws_db_instance" "mysql" {
  identifier         = "mydb"
  engine             = "mysql"
  instance_class     = "db.t3.micro"
  allocated_storage  = 20
  username           = "admin"
  password           = "securepass123"
  skip_final_snapshot = true
}
```

**Tips & Best Practices:**
- Use `parameter groups` for tuning.
- Enable backups and multi-AZ for production.

**Example:**
```bash
terraform apply -auto-approve
```

---

### **Question 14: How do you automate CloudFormation stack creation from a template file?**

**Answer:**
```bash
aws cloudformation create-stack \
  --stack-name my-stack \
  --template-body file://template.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

**Tips & Best Practices:**
- Use `change sets` for safe updates.
- Validate templates before deployment.

**Example:**
```bash
aws cloudformation validate-template --template-body file://template.yaml
```

---

### **Question 15: How do you configure Auto Scaling for EC2 based on CPU usage?**

**Answer:**
1. Create launch template.
2. Create Auto Scaling group.
3. Add scaling policy:
   ```bash
   aws autoscaling put-scaling-policy \
     --auto-scaling-group-name my-asg \
     --policy-name cpu-scale-out \
     --adjustment-type ChangeInCapacity \
     --scaling-adjustment 1 \
     --cooldown 300
   ```

**Tips & Best Practices:**
- Use target tracking for smoother scaling.
- Monitor with CloudWatch alarms.

**Example:**
```bash
aws autoscaling describe-auto-scaling-groups
```

---

### **Question 16: How do you restrict access to an S3 bucket to only a specific VPC endpoint?**

**Answer:**
1. Create VPC endpoint for S3.
2. Add bucket policy:
   ```json
   {
     "Effect": "Deny",
     "Principal": "*",
     "Action": "s3:*",
     "Resource": "arn:aws:s3:::mybucket/*",
     "Condition": {
       "StringNotEquals": {
         "aws:SourceVpce": "vpce-12345678"
       }
     }
   }
   ```

**Tips & Best Practices:**
- Use private DNS for seamless access.
- Audit with CloudTrail.

**Example:**
```bash
aws s3 ls s3://mybucket --endpoint-url https://vpce-12345678.s3.us-east-1.vpce.amazonaws.com
```

---

### **Question 17: How do you rotate IAM access keys automatically using AWS CLI and cron?**

**Answer:**
1. Create new key:
   ```bash
   aws iam create-access-key --user-name devuser
   ```

2. Update credentials file.

3. Delete old key:
   ```bash
   aws iam delete-access-key --user-name devuser --access-key-id OLDKEY123
   ```

**Tips & Best Practices:**
- Use IAM roles instead of keys when possible.
- Store keys securely (e.g., Secrets Manager).

**Example:**
```bash
aws configure set aws_access_key_id NEWKEY123
```

---

### **Question 18: How do you deploy infrastructure using CloudFormation nested stacks?**

**Answer:**
1. Create parent template:
   ```yaml
   Resources:
     VPCStack:
       Type: AWS::CloudFormation::Stack
       Properties:
         TemplateURL: https://s3.amazonaws.com/templates/vpc.yaml
   ```

2. Deploy parent stack.

**Tips & Best Practices:**
- Use nested stacks for modular design.
- Version templates in S3.

**Example:**
```bash
aws cloudformation deploy --template-file parent.yaml --stack-name infra-stack
```

---

### **Question 19: How do you enable detailed monitoring for EC2 instances?**

**Answer:**
1. Enable via CLI:
   ```bash
   aws ec2 monitor-instances --instance-ids i-12345678
   ```

2. View metrics in CloudWatch.

**Tips & Best Practices:**
- Use detailed monitoring for production workloads.
- Tag instances for grouping.

**Example:**
```bash
aws cloudwatch get-metric-statistics --metric-name CPUUtilization ...
```

---

### **Question 20: How do you automate Lambda deployment with Terraform and S3?**

**Answer:**
1. Upload ZIP to S3.
2. Terraform:
   ```hcl
   resource "aws_lambda_function" "app" {
     function_name = "my-lambda"
     s3_bucket     = "lambda-code"
     s3_key        = "app.zip"
     handler       = "index.handler"
     runtime       = "nodejs18.x"
     role          = aws_iam_role.lambda_exec.arn
   }
   ```

**Tips & Best Practices:**
- Use versioned S3 keys.
- Automate with CI/CD pipelines.

**Example:**
```bash
terraform apply
```

---

You're on a roll, Mahesh! Here's the next batch of **AWS DevOps Scenario-Based Questions (21–30)** — covering IAM roles, EKS, CloudFront, Secrets Manager, and production-grade automation. These are designed for scalable, secure, and efficient cloud operations.

---

## ☁️ AWS DevOps Scenario-Based Questions (21–30)

---

### **Question 21: How do you create an IAM role for EC2 to access S3 securely?**

**Answer:**
1. Create IAM role with trust policy:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Effect": "Allow",
       "Principal": { "Service": "ec2.amazonaws.com" },
       "Action": "sts:AssumeRole"
     }]
   }
   ```

2. Attach S3 access policy:
   ```json
   {
     "Effect": "Allow",
     "Action": ["s3:GetObject", "s3:ListBucket"],
     "Resource": ["arn:aws:s3:::mybucket", "arn:aws:s3:::mybucket/*"]
   }
   ```

3. Attach role to EC2 instance.

**Tips & Best Practices:**
- Prefer roles over access keys.
- Use least privilege principle.

**Example:**
```bash
aws ec2 associate-iam-instance-profile \
  --instance-id i-12345678 \
  --iam-instance-profile Name=EC2S3Access
```

---

### **Question 22: How do you deploy a Kubernetes cluster on AWS using EKS and Terraform?**

**Answer:**
1. Use Terraform EKS module:
   ```hcl
   module "eks" {
     source          = "terraform-aws-modules/eks/aws"
     cluster_name    = "my-cluster"
     cluster_version = "1.28"
     subnets         = var.subnets
     vpc_id          = var.vpc_id
   }
   ```

2. Apply and configure `kubectl`:
   ```bash
   terraform apply
   aws eks update-kubeconfig --name my-cluster
   ```

**Tips & Best Practices:**
- Use IRSA for secure pod access to AWS services.
- Monitor with Prometheus + Grafana.

**Example:**
```bash
kubectl get nodes
```

---

### **Question 23: How do you configure CloudFront to serve content from an S3 bucket securely?**

**Answer:**
1. Create S3 bucket with static website hosting.
2. Create CloudFront distribution:
   - Origin: S3 bucket
   - Restrict bucket access
   - Use OAI (Origin Access Identity)

**Tips & Best Practices:**
- Enable caching and compression.
- Use HTTPS with custom domain.

**Example:**
```bash
aws cloudfront create-distribution --origin-domain-name mybucket.s3.amazonaws.com
```

---

### **Question 24: How do you store and retrieve secrets securely using AWS Secrets Manager?**

**Answer:**
1. Store secret:
   ```bash
   aws secretsmanager create-secret \
     --name MyDBSecret \
     --secret-string '{"username":"admin","password":"securepass"}'
   ```

2. Retrieve secret:
   ```bash
   aws secretsmanager get-secret-value --secret-id MyDBSecret
   ```

**Tips & Best Practices:**
- Rotate secrets automatically.
- Use IAM policies to restrict access.

**Example:**
```bash
aws secretsmanager rotate-secret --secret-id MyDBSecret
```

---

### **Question 25: How do you configure lifecycle policies for S3 to transition objects to Glacier?**

**Answer:**
1. Create lifecycle rule:
   ```json
   {
     "Rules": [{
       "ID": "GlacierTransition",
       "Status": "Enabled",
       "Filter": { "Prefix": "" },
       "Transitions": [{
         "Days": 30,
         "StorageClass": "GLACIER"
       }]
     }]
   }
   ```

2. Apply via CLI:
   ```bash
   aws s3api put-bucket-lifecycle-configuration \
     --bucket mybucket \
     --lifecycle-configuration file://lifecycle.json
   ```

**Tips & Best Practices:**
- Use expiration for cleanup.
- Monitor storage class transitions.

**Example:**
```bash
aws s3api get-bucket-lifecycle-configuration --bucket mybucket
```

---

### **Question 26: How do you automate AMI creation from an EC2 instance using AWS CLI?**

**Answer:**
```bash
aws ec2 create-image \
  --instance-id i-12345678 \
  --name "MyAppAMI" \
  --description "AMI with latest app version" \
  --no-reboot
```

**Tips & Best Practices:**
- Tag AMIs with version and environment.
- Clean up unused AMIs regularly.

**Example:**
```bash
aws ec2 describe-images --owners self
```

---

### **Question 27: How do you configure an Application Load Balancer (ALB) with path-based routing using Terraform?**

**Answer:**
1. Define listener rules:
   ```hcl
   resource "aws_lb_listener_rule" "api" {
     listener_arn = aws_lb_listener.front_end.arn
     priority     = 100
     actions {
       type             = "forward"
       target_group_arn = aws_lb_target_group.api.arn
     }
     conditions {
       path_pattern {
         values = ["/api/*"]
       }
     }
   }
   ```

**Tips & Best Practices:**
- Use health checks for target groups.
- Tag resources for visibility.

**Example:**
```bash
terraform apply
```

---

### **Question 28: How do you enable logging for Lambda functions to CloudWatch?**

**Answer:**
1. Ensure IAM role has:
   ```json
   {
     "Effect": "Allow",
     "Action": ["logs:CreateLogGroup", "logs:CreateLogStream", "logs:PutLogEvents"],
     "Resource": "*"
   }
   ```

2. Logs appear in `/aws/lambda/<function-name>`.

**Tips & Best Practices:**
- Use structured logging (JSON).
- Set retention policies.

**Example:**
```bash
aws logs describe-log-groups --log-group-name-prefix /aws/lambda
```

---

### **Question 29: How do you set up an EBS volume and attach it to a running EC2 instance using CLI?**

**Answer:**
1. Create volume:
   ```bash
   aws ec2 create-volume --availability-zone us-east-1a --size 20 --volume-type gp2
   ```

2. Attach to instance:
   ```bash
   aws ec2 attach-volume --volume-id vol-12345678 --instance-id i-12345678 --device /dev/sdf
   ```

**Tips & Best Practices:**
- Format and mount volume after attachment.
- Use tags for identification.

**Example:**
```bash
sudo mkfs -t ext4 /dev/xvdf
sudo mount /dev/xvdf /mnt/data
```

---

### **Question 30: How do you configure an EC2 launch template for Auto Scaling groups using Terraform?**

**Answer:**
```hcl
resource "aws_launch_template" "web" {
  name_prefix   = "web-template-"
  image_id      = "ami-0abc123"
  instance_type = "t3.micro"
  key_name      = "my-key"
  user_data     = base64encode(file("init.sh"))
}
```

**Tips & Best Practices:**
- Use launch templates over launch configurations.
- Version templates for rollback.

**Example:**
```bash
terraform apply
```

---
Perfect, Mahesh — let’s dive into **AWS DevOps Scenario-Based Questions (31–40)** with full explanations. These cover CloudTrail, WAF, Route 53, Elastic Beanstalk, and more. I’ll break down not just the how, but the why behind each solution.

---

## ☁️ AWS DevOps Scenario-Based Questions (31–40)

---

### **Question 31: How do you enable CloudTrail to audit all AWS account activity?**

**Answer:**
CloudTrail records API calls and events across your AWS account. It’s essential for security audits, compliance, and incident response.

1. Enable CloudTrail:
   ```bash
   aws cloudtrail create-trail \
     --name org-trail \
     --s3-bucket-name cloudtrail-logs-bucket \
     --is-multi-region-trail
   ```

2. Start logging:
   ```bash
   aws cloudtrail start-logging --name org-trail
   ```

**Tips & Best Practices:**
- Use a dedicated S3 bucket with encryption and access logging.
- Enable log file validation for integrity.

**Example:**
```bash
aws cloudtrail describe-trails
```

---

### **Question 32: How do you protect a web application using AWS WAF with rate limiting?**

**Answer:**
AWS WAF helps block malicious traffic and rate-limit abusive requests.

1. Create a Web ACL:
   ```bash
   aws wafv2 create-web-acl --name "RateLimitACL" ...
   ```

2. Add a rate-based rule:
   ```json
   {
     "Name": "RateLimitRule",
     "Priority": 1,
     "Action": { "Block": {} },
     "Statement": {
       "RateBasedStatement": {
         "Limit": 1000,
         "AggregateKeyType": "IP"
       }
     }
   }
   ```

3. Associate with CloudFront or ALB.

**Tips & Best Practices:**
- Combine with IP reputation lists.
- Monitor blocked requests via CloudWatch.

**Example:**
```bash
aws wafv2 list-web-acls --scope CLOUDFRONT
```

---

### **Question 33: How do you configure Route 53 to route traffic based on geographic location?**

**Answer:**
Geo DNS lets you serve region-specific content or route users to the nearest endpoint.

1. Create a hosted zone.
2. Add records with location routing:
   ```bash
   aws route53 change-resource-record-sets --hosted-zone-id ZONEID --change-batch file://geo.json
   ```

**Tips & Best Practices:**
- Use fallback records for unsupported regions.
- Combine with latency-based routing for performance.

**Example:**
```json
{
  "GeoLocation": {
    "CountryCode": "IN"
  },
  "ResourceRecords": [{ "Value": "192.0.2.1" }]
}
```

---

### **Question 34: How do you deploy a web app using Elastic Beanstalk with a custom environment?**

**Answer:**
Elastic Beanstalk abstracts infrastructure while letting you customize environments.

1. Initialize:
   ```bash
   eb init -p node.js my-app
   ```

2. Create environment:
   ```bash
   eb create my-env --instance_type t3.micro --scale 2
   ```

3. Deploy:
   ```bash
   eb deploy
   ```

**Tips & Best Practices:**
- Use `.ebextensions` for advanced config.
- Monitor health via EB dashboard.

**Example:**
```yaml
# .ebextensions/logging.config
option_settings:
  aws:elasticbeanstalk:application:environment:
    LOG_LEVEL: DEBUG
```

---

### **Question 35: How do you enforce MFA for IAM users using a policy?**

**Answer:**
MFA adds a second layer of security. You can enforce it using a condition in IAM policies.

1. Attach policy:
   ```json
   {
     "Effect": "Deny",
     "Action": "*",
     "Resource": "*",
     "Condition": {
       "BoolIfExists": {
         "aws:MultiFactorAuthPresent": "false"
       }
     }
   }
   ```

**Tips & Best Practices:**
- Use IAM groups for scalable enforcement.
- Audit MFA status regularly.

**Example:**
```bash
aws iam list-virtual-mfa-devices
```

---

### **Question 36: How do you automate EC2 instance termination after a fixed time using Lambda?**

**Answer:**
This is useful for cost control in dev/test environments.

1. Create a Lambda function with EC2 termination logic:
   ```python
   import boto3
   ec2 = boto3.client('ec2')
   ec2.terminate_instances(InstanceIds=['i-12345678'])
   ```

2. Trigger via CloudWatch Events:
   ```bash
   aws events put-rule --schedule-expression "rate(1 hour)"
   ```

**Tips & Best Practices:**
- Tag instances with expiration metadata.
- Log actions for audit.

**Example:**
```bash
aws lambda invoke --function-name TerminateEC2 result.json
```

---

### **Question 37: How do you configure an S3 bucket for static website hosting with custom error pages?**

**Answer:**
S3 can serve static content directly, ideal for SPAs or documentation sites.

1. Enable website hosting:
   ```bash
   aws s3 website s3://mybucket/ --index-document index.html --error-document error.html
   ```

2. Set bucket policy for public access:
   ```json
   {
     "Effect": "Allow",
     "Principal": "*",
     "Action": "s3:GetObject",
     "Resource": "arn:aws:s3:::mybucket/*"
   }
   ```

**Tips & Best Practices:**
- Use CloudFront for HTTPS and caching.
- Avoid public access for sensitive buckets.

**Example:**
```bash
curl http://mybucket.s3-website-us-east-1.amazonaws.com
```

---

### **Question 38: How do you create a CloudWatch dashboard to monitor EC2 and RDS metrics?**

**Answer:**
Dashboards help visualize performance and health across services.

1. Create dashboard:
   ```bash
   aws cloudwatch put-dashboard \
     --dashboard-name InfraDashboard \
     --dashboard-body file://dashboard.json
   ```

2. Include widgets for CPU, memory, IOPS.

**Tips & Best Practices:**
- Use dynamic widgets for multi-instance views.
- Share dashboards with teams.

**Example:**
```json
{
  "widgets": [{
    "type": "metric",
    "properties": {
      "metrics": [["AWS/EC2", "CPUUtilization", "InstanceId", "i-12345678"]],
      "title": "EC2 CPU"
    }
  }]
}
```

---

### **Question 39: How do you configure AWS Backup to protect EFS and RDS resources?**

**Answer:**
AWS Backup centralizes backup management across services.

1. Create backup plan:
   ```bash
   aws backup create-backup-plan --backup-plan file://plan.json
   ```

2. Assign resources:
   ```bash
   aws backup create-backup-selection --backup-plan-id PLANID --backup-selection file://selection.json
   ```

**Tips & Best Practices:**
- Use tags to group resources.
- Enable cross-region backup for DR.

**Example:**
```json
{
  "SelectionName": "MySelection",
  "Resources": ["arn:aws:efs:..."]
}
```

---

### **Question 40: How do you use Systems Manager Parameter Store to inject config into EC2 or Lambda?**

**Answer:**
Parameter Store is a secure way to manage environment variables and secrets.

1. Store parameter:
   ```bash
   aws ssm put-parameter --name "/app/db/password" --value "securepass" --type SecureString
   ```

2. Access from EC2:
   ```bash
   aws ssm get-parameter --name "/app/db/password" --with-decryption
   ```

3. Inject into Lambda via environment variables.

**Tips & Best Practices:**
- Use path-based naming for organization.
- Set IAM policies to restrict access.

**Example:**
```bash
aws ssm describe-parameters --parameter-filters Key=Name,Option=BeginsWith,Values=/app/
```

---
Awesome, Mahesh — here’s the final batch of **AWS DevOps Scenario-Based Questions (41–50)** with full explanations. These focus on advanced networking, disaster recovery, cost optimization, and cross-account automation — the kind of work that defines senior-level DevOps mastery.

---

## ☁️ AWS DevOps Scenario-Based Questions (41–50)

---

### **Question 41: How do you set up a VPC peering connection between two AWS accounts using CLI?**

**Answer:**
VPC peering allows private communication between VPCs across accounts or regions.

1. Request peering from Account A:
   ```bash
   aws ec2 create-vpc-peering-connection \
     --vpc-id vpc-aaa111 \
     --peer-vpc-id vpc-bbb222 \
     --peer-owner-id 123456789012
   ```

2. Accept peering in Account B:
   ```bash
   aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id pcx-12345678
   ```

3. Update route tables in both VPCs.

**Why it matters:**  
This enables secure, low-latency communication between services in different accounts without exposing them to the internet.

**Tips & Best Practices:**
- Avoid overlapping CIDR blocks.
- Use tags to track peering relationships.

**Example:**
```bash
aws ec2 describe-vpc-peering-connections
```

---

### **Question 42: How do you implement cross-region disaster recovery for an RDS instance?**

**Answer:**
1. Enable automated backups.
2. Create a snapshot:
   ```bash
   aws rds create-db-snapshot --db-instance-identifier mydb --db-snapshot-identifier mydb-snap
   ```

3. Copy snapshot to another region:
   ```bash
   aws rds copy-db-snapshot \
     --source-db-snapshot-identifier mydb-snap \
     --target-db-snapshot-identifier mydb-snap-dr \
     --source-region us-east-1 \
     --region ap-south-1
   ```

**Why it matters:**  
This ensures you can restore critical databases in another region during outages or disasters.

**Tips & Best Practices:**
- Automate snapshot copying with Lambda + EventBridge.
- Encrypt snapshots for security.

**Example:**
```bash
aws rds restore-db-instance-from-db-snapshot --db-snapshot-identifier mydb-snap-dr --db-instance-identifier mydb-dr
```

---

### **Question 43: How do you use AWS Cost Explorer to identify underutilized resources?**

**Answer:**
1. Enable Cost Explorer in the console.
2. Use CLI to fetch usage data:
   ```bash
   aws ce get-cost-and-usage \
     --time-period Start=2025-09-01,End=2025-09-30 \
     --granularity MONTHLY \
     --metrics "UnblendedCost" \
     --group-by Type=DIMENSION,Key=SERVICE
   ```

**Why it matters:**  
This helps reduce waste and optimize spend by identifying idle or oversized resources.

**Tips & Best Practices:**
- Tag resources by environment (e.g., dev, prod).
- Use Trusted Advisor for deeper insights.

**Example:**
```bash
aws ce get-reservation-utilization
```

---

### **Question 44: How do you automate cross-account access using IAM roles and STS?**

**Answer:**
1. In Account A, create a role with trust policy:
   ```json
   {
     "Effect": "Allow",
     "Principal": { "AWS": "arn:aws:iam::AccountB-ID:root" },
     "Action": "sts:AssumeRole"
   }
   ```

2. In Account B, assume the role:
   ```bash
   aws sts assume-role \
     --role-arn arn:aws:iam::AccountA-ID:role/CrossAccountRole \
     --role-session-name DevOpsSession
   ```

**Why it matters:**  
This enables secure automation across accounts without sharing credentials.

**Tips & Best Practices:**
- Use session policies to limit access.
- Rotate trust relationships periodically.

**Example:**
```bash
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
```

---

### **Question 45: How do you configure Transit Gateway to connect multiple VPCs?**

**Answer:**
1. Create Transit Gateway:
   ```bash
   aws ec2 create-transit-gateway --description "Central Hub"
   ```

2. Attach VPCs:
   ```bash
   aws ec2 create-transit-gateway-vpc-attachment \
     --transit-gateway-id tgw-12345678 \
     --vpc-id vpc-aaa111 \
     --subnet-ids subnet-xyz123
   ```

3. Update route tables.

**Why it matters:**  
Transit Gateway simplifies complex network topologies by acting as a central router.

**Tips & Best Practices:**
- Use route propagation for dynamic routing.
- Monitor with Transit Gateway Flow Logs.

**Example:**
```bash
aws ec2 describe-transit-gateway-attachments
```

---

### **Question 46: How do you use AWS Config to track changes to security groups?**

**Answer:**
1. Enable AWS Config:
   ```bash
   aws configservice put-configuration-recorder \
     --configuration-recorder name=default,roleARN=arn:aws:iam::123456789012:role/config-role
   ```

2. Start recording:
   ```bash
   aws configservice start-configuration-recorder --configuration-recorder-name default
   ```

3. Query changes:
   ```bash
   aws configservice get-resource-config-history --resource-type AWS::EC2::SecurityGroup --resource-id sg-12345678
   ```

**Why it matters:**  
This provides visibility into configuration drift and unauthorized changes.

**Tips & Best Practices:**
- Set up SNS notifications for changes.
- Use Config Rules for compliance enforcement.

**Example:**
```bash
aws configservice describe-config-rules
```

---

### **Question 47: How do you use AWS Step Functions to orchestrate a multi-step deployment?**

**Answer:**
1. Define state machine:
   ```json
   {
     "StartAt": "Build",
     "States": {
       "Build": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:...:function:BuildApp",
         "Next": "Deploy"
       },
       "Deploy": {
         "Type": "Task",
         "Resource": "arn:aws:lambda:...:function:DeployApp",
         "End": true
       }
     }
   }
   ```

2. Deploy via CLI:
   ```bash
   aws stepfunctions create-state-machine --name DeployFlow --definition file://flow.json --role-arn arn:aws:iam::role/step-role
   ```

**Why it matters:**  
Step Functions provide visual, fault-tolerant orchestration for complex workflows.

**Tips & Best Practices:**
- Use retries and catch blocks.
- Monitor execution history.

**Example:**
```bash
aws stepfunctions start-execution --state-machine-arn arn:aws:states:... --name run-001
```

---

### **Question 48: How do you use AWS Inspector to scan EC2 instances for vulnerabilities?**

**Answer:**
1. Install SSM agent and ensure EC2 is managed.
2. Enable Inspector:
   ```bash
   aws inspector2 enable --resource-types EC2
   ```

3. Run scan:
   ```bash
   aws inspector2 list-findings
   ```

**Why it matters:**  
Inspector helps detect CVEs and misconfigurations in real time.

**Tips & Best Practices:**
- Integrate with Security Hub.
- Remediate findings via automation.

**Example:**
```bash
aws inspector2 describe-organization-configuration
```

---

### **Question 49: How do you use AWS Control Tower to manage multi-account governance?**

**Answer:**
Control Tower sets up landing zones with guardrails and account vending.

1. Enable Control Tower via console.
2. Create organizational units (OUs).
3. Provision accounts with baseline policies.

**Why it matters:**  
It simplifies multi-account setup with governance, logging, and security baked in.

**Tips & Best Practices:**
- Use SCPs to enforce policies.
- Monitor with AWS Audit account.

**Example:**
```bash
aws organizations list-accounts
```

---

### **Question 50: How do you use AWS Trusted Advisor to optimize cost and security?**

**Answer:**
1. Access via console or CLI:
   ```bash
   aws support describe-trusted-advisor-checks --language en
   ```

2. Get check results:
   ```bash
   aws support describe-trusted-advisor-check-result --check-id eW7HH0l7J9
   ```

**Why it matters:**  
Trusted Advisor provides actionable insights on cost, performance, fault tolerance, and security.

**Tips & Best Practices:**
- Schedule regular reviews.
- Automate remediation with Lambda.

**Example:**
```bash
aws support refresh-trusted-advisor-check --check-id eW7HH0l7J9
```

---
