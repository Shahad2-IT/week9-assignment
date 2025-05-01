# Clarusway Week 9 AWS Assignment

## Scenario
Deploy a highly available web application using:
- S3 for static assets
- Auto Scaling Group (ASG) for NGINX servers
- Application Load Balancer (ALB) for traffic distribution

---

## Part 1: S3 Setup (Static Assets)
- Created S3 bucket: shahad-clarusway-assets1 in eu-north-1
- Uploaded:
  - index.html
  - logo.png
  - sda.png
- Enabled static website hosting
- Added public read access bucket policy
- Verified access: ✅

---

## Part 2: Auto Scaling Group (ASG)

### VPC & Networking:
- Created VPC: clarusway-vpc1 with CIDR: 10.0.0.0/16
- Created subnets:
  - clarusway-subnet-1a → 10.0.1.0/24
  - clarusway-subnet-1b → 10.0.2.0/24
- Created and attached an Internet Gateway
- Created route table clarusway-rt1 and associated it with both subnets
- Added route 0.0.0.0/0 → IGW

### Launch Template:
- Name: clarusway-template1
- AMI: Amazon Linux 2
- Type: t3.micro
- Key Pair: shahad2023
- Security Group: clarusway-sg1 (allows HTTP 80, SSH 22)
- User Data:
```bash
#!/bin/bash
yum update -y
amazon-linux-extras enable nginx1
yum install nginx -y
systemctl start nginx
systemctl enable nginx
aws s3 cp s3://shahad-clarusway-assets1/index.html /tmp/index.html
mv /tmp/index.html /usr/share/nginx/html/index.html

Auto Scaling Group:
 • Name: clarusway-asg1
 • Desired: 2 | Min: 1 | Max: 3
 • Attached to both subnets
 • Health Checks: EC2 + ELB

Part 3: Application Load Balancer (ALB)
 • Name: clarusway-alb1
 • Type: Internet-facing
 • Protocol: HTTP (port 80)
 • Subnets: 1a1 and 1b1
 • Target Group: clarusway-tg1 (Instance type, port 80, path /)
 • ALB linked to ASG
 • Verified DNS endpoint and health check

Testing Load Balancing
Verified response from both instances:
for i in {1..5}; do curl -s http://clarusway-alb1-1483609001.eu-north-1.elb.amazonaws.com | grep "Hostname"; done
Output shows alternating instance hostnames confirming proper distribution.


