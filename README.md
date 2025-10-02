

---


---

## `README.md` (paste this into your GitHub repo)
# Deploy Application using Auto Scaling Group

> This README is based on the uploaded guide `Deploy application using autoscalling group.pdf`. 

## Project summary
This project demonstrates how to deploy a simple web application on AWS using an **Application Load Balancer (ALB)** and an **Auto Scaling Group (ASG)**. It includes steps to create the target group, ALB, Launch Template (with user-data bootstrap), ASG configuration, and optional SNS notifications for lifecycle events.

---

## 📑 Project Report
👉 [Click here to view/download the full PDF][[[(https://drive.google.com/your-public-share-link)]](https://drive.google.com/file/d/1D2aWxYENuburDxGCn5jcYsyxSChBVlbs/view)](https://drive.google.com/file/d/1EttWhE3w-DLWnlGAiARdfLD67xl3Tfgq/view)

---
---

## Table of contents
- [Step-by-step deployment](#step-by-step-deployment)
  - [1. Create Target Group](#1-create-target-group)
  - [2. Create Application Load Balancer](#2-create-application-load-balancer)
  - [3. Create Launch Template (user-data)](#3-create-launch-template-user-data)
  - [4. Create Auto Scaling Group](#4-create-auto-scaling-group)
  - [5. SNS notifications (optional)](#5-sns-notifications-optional)
- [Verify & test](#verify--test)


---


---

## Architecture
- **ALB** receives incoming traffic and forwards to the **Target Group**.
- **Auto Scaling Group** launches EC2 instances (based on Launch Template) and registers them to the Target Group.
- **SNS** (optional) receives notifications for ASG lifecycle events (e.g., failed launches).

---

## Step-by-step deployment

### 1. Create Target Group
1. Go to EC2 → Target Groups → Create target group.
2. Choose **Target type**: `instance` (or `ip` if you need).
3. Protocol: `HTTP` (or `HTTPS` if terminating TLS).
4. Health check path: `/` or `/health`.
5. Note the Target Group ARN/name for later. 

### 2. Create Application Load Balancer
1. EC2 → Load Balancers → Create Application Load Balancer.
2. Choose **internet-facing** (if public) and select the VPC + public subnets across AZs.
3. Add listener (HTTP 80 / HTTPS 443).
4. Configure listener rules to forward to the Target Group created earlier.
5. Configure security group allowing HTTP/HTTPS from appropriate sources. 

### 3. Create Launch Template (with user-data)
1. EC2 → Launch Templates → Create Launch Template.
2. Select AMI, instance type, key pair, IAM role (recommended), and security group.
3. Add **User data** (bootstrap script) to install and start the app. Example:
````markdown


```bash
#!/bin/bash
yum update -y
yum install -y nginx
cat > /usr/share/nginx/html/index.html <<EOF
<html><body><h1>Deployed via Auto Scaling Group</h1></body></html>
EOF
systemctl enable nginx
systemctl start nginx
````

4. Save the Launch Template and note the ID.

### 4. Create Auto Scaling Group

1. EC2 → Auto Scaling Groups → Create Auto Scaling Group.
2. Choose the Launch Template created in step 3.
3. Select the VPC and appropriate subnets (same AZs as ALB).
4. Attach the Target Group (so ALB health checks determine instance state).
5. Health check type: **ELB** (recommended when using ALB).
6. Set capacity: `min`, `desired`, `max` (example values in guide: `min = 2`, `desired = 4`, `max = 6` — adjust per your needs).
7. Configure scaling policies if needed (target tracking or step scaling).
8. Add tags and lifecycle hooks if you need graceful shutdown or notifications.

### 5. SNS notifications (optional)

1. Create SNS topic and subscriber (email/SMS/webhook).
2. Configure Auto Scaling lifecycle hooks or CloudWatch alarms to publish events (e.g., failed launch, instance termination) to the SNS topic.
3. Validate that subscribers receive notifications.

---

## Verify & test

* Open the ALB DNS name in browser → you should see the app page.
* EC2 console → Target Groups → check instances are `InService` and `healthy`.
* Simulate load or change ASG desired capacity to test scale-out/scale-in behavior.
* Confirm SNS notifications arrive if configured.

---




---



```

---



Which one do you want next? (No extra scans — this README uses only the recent PDF.)
```
