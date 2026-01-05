Perfect 👍
**Phase 2 is the foundation that makes Phase 3 make sense.**
This is where you stop “clicking around AWS” and start **thinking in AWS primitives**.

I’ll keep the style **exactly the same** as Phase 3: structured, practical, and outcome-driven.

---

# ✅ Phase 2: Core AWS Infrastructure & Networking — TODO Checklist

⏱️ Target time: **2–3 weeks**
🎯 Goal: *You understand how AWS actually works under the hood (networking, IAM, compute, storage) — not just how to launch things*

---

## 🧠 A. AWS Global Architecture (Mental Model)

☐ Understand AWS hierarchy:

* Account
* Region
* Availability Zone (AZ)

☐ Know **why regions exist**

* Latency
* Compliance
* Fault isolation

☐ Know which resources are:

* Global (IAM)
* Regional (VPC, ALB)
* AZ-specific (EC2, EBS)

📌 You should explain:

> “Why my EC2 in ap-southeast-2 can’t see a resource in ap-northeast-1”

---

## 👤 B. IAM (Identity & Access Management) — VERY IMPORTANT

☐ Understand IAM entities:

* User
* Group
* Role
* Policy

☐ Difference between:

* Authentication vs Authorization
* Identity-based vs Resource-based policies

☐ Create:

* IAM user (no root usage)
* IAM group with least privilege
* IAM role for EC2

☐ Attach policies:

* Managed vs inline
* ReadOnlyAccess vs AdminAccess

📌 Explain:

> “Why my app gets 403 even though it runs on EC2”

---

## 🖥️ C. EC2 Fundamentals (Compute Core)

☐ Understand EC2 lifecycle:

* Launch
* Stop
* Terminate

☐ Know instance types:

* t3 / t4g (burstable)
* m / c / r families

☐ Understand pricing:

* On-Demand
* Spot
* Reserved

☐ Launch EC2 with:

* Key pair
* Security group
* IAM role

☐ SSH into instance:

```bash
ssh -i key.pem ec2-user@<public-ip>
```

📌 You should know **why stopping EC2 reduces cost**

---

## 🔐 D. Security Groups vs NACLs

☐ Understand:

* Security Group = stateful
* NACL = stateless

☐ Allow:

* SSH (22)
* HTTP (80)
* HTTPS (443)

☐ Debug:

* “Connection timeout”
* “Connection refused”

📌 Explain:

> “Why my instance is running but unreachable”

---

## 🌐 E. VPC Fundamentals (This Is Critical)

☐ Understand VPC components:

* CIDR block
* Subnets
* Route tables
* Internet Gateway
* NAT Gateway

☐ Create:

* VPC
* Public subnet
* Private subnet

☐ Understand:

* Public subnet ≠ public instance
* Route table decides reachability

📌 This explains **90% of AWS networking issues**

---

## 🛣️ F. Routing & Internet Access

☐ Attach Internet Gateway
☐ Route `0.0.0.0/0` to IGW
☐ Verify public EC2 access

☐ Create NAT Gateway
☐ Route private subnet traffic via NAT

☐ Understand why NAT costs money

📌 You already hit this in EKS — now you *own it*

---

## 📦 G. Storage Fundamentals

### 🧱 EBS (Block Storage)

☐ Attach EBS volume to EC2
☐ Mount volume manually
☐ Understand:

* AZ binding
* Snapshot backups

### 🪣 S3 (Object Storage)

☐ Create S3 bucket
☐ Upload/download objects
☐ Understand:

* Bucket policy
* IAM access vs public access

📌 Explain:

> “Why S3 is not a filesystem”

---

## ⚖️ H. Load Balancing Basics

☐ Understand load balancer types:

* ALB (HTTP/HTTPS)
* NLB (TCP)
* CLB (legacy)

☐ Create ALB
☐ Register EC2 targets
☐ Verify traffic distribution

☐ Understand health checks

📌 This prepares you for Kubernetes Services

---

## 📜 I. Logging & Monitoring Basics

☐ Understand CloudWatch:

* Logs
* Metrics
* Alarms

☐ Send EC2 logs to CloudWatch
☐ View:

* CPUUtilization
* NetworkIn/Out

☐ Create alarm for high CPU

📌 Observability starts here

---

## 🔑 J. AWS CLI & SDK Usage

☐ Configure CLI:

```bash
aws configure
```

☐ Use profiles
☐ List resources:

```bash
aws ec2 describe-instances
aws iam list-users
aws s3 ls
```

☐ Understand:

* Access key rotation
* Environment variables

📌 CLI > Console for serious work

---

## 🧪 K. Debugging AWS Issues (Core Skill)

☐ Identify IAM permission errors
☐ Debug networking issues
☐ Distinguish:

* Security Group issue
* Route table issue
* DNS issue

☐ Use:

```bash
curl
ping
traceroute
```

📌 Same skillset you use later in EKS

---

## 💰 L. Cost Awareness & Control

☐ Understand AWS billing model
☐ Identify cost drivers:

* EC2
* NAT Gateway
* Load Balancer
* EBS

☐ Enable billing access for IAM users
☐ Set budget alerts

☐ Clean unused resources

📌 Cost control = production responsibility

---

## 🧪 M. Phase 2 Hands-On Mini Projects

☐ Public EC2 web server
☐ Private EC2 via NAT
☐ S3 static website
☐ EC2 behind ALB
☐ Secure EC2 with IAM role

📌 No Kubernetes yet — pure AWS

---

## 🎯 Phase 2 Exit Criteria

✅ You understand AWS networking
✅ IAM errors don’t scare you
✅ You know what costs money
✅ You can design a secure VPC
✅ You can debug AWS infra issues

---

## 🔜 What Phase 2 Unlocks

➡ Phase 3 (Kubernetes) suddenly makes sense
➡ You stop guessing and start reasoning
➡ AWS feels *predictable*, not magical

---

If you want next, I can:

* Turn Phase 2 into **daily tasks**
* Give **intentional failure labs**
* Map this to **AWS SAA certification**
* Connect Phase 2 directly to **your EKS mistakes** (real examples)

You’re building this the **right way** 💪
