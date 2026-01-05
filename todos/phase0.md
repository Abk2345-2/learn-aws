Perfect — **Phase 0 is the foundation**.
If Phase 0 is weak, *everything later feels confusing and expensive*.
This TODO list is designed so Phase 1 (Networking) feels **easy**, not overwhelming.

---

# ✅ Phase 0: AWS Foundations — TODO Checklist

⏱️ Target time: **5–7 days**
🎯 Goal: *You are comfortable with AWS account structure, IAM, CLI, regions, and cost safety*

---

## 🧠 A. AWS Mental Model (Must Internalize)

☐ Understand what **AWS really is**

* Collection of managed services
* You pay for **resources + time + traffic**

☐ Learn AWS **global infrastructure**

* Regions
* Availability Zones
* Edge locations

☐ Understand:

* Why regions exist
* Why AZs are isolated
* Why latency matters

📌 You should confidently explain:

> “Why should production never run in one AZ?”

---

## 🧾 B. AWS Account & Billing Safety (CRITICAL)

☐ Enable **MFA** on root account
☐ Lock away root credentials (never use daily)

☐ Create **IAM Admin user**

* Programmatic + Console access
* AdministratorAccess policy

☐ Enable **Billing alerts**

* Free tier usage alerts
* Budget alert ($5–$10)

☐ Enable **Cost Explorer**

📌 If this isn’t done → **STOP everything else**

---

## 👤 C. IAM Fundamentals (Non-Negotiable)

☐ Understand IAM building blocks:

* Users
* Groups
* Roles
* Policies

☐ Learn difference:

* Identity-based policies
* Resource-based policies

☐ Create IAM Groups:

* `admins`
* `developers`
* `readonly`

☐ Attach least-privilege policies

☐ Create **IAM Role**

* EC2 role with S3 read-only access

📌 Explain:

> “Why roles are safer than access keys”

---

## 💻 D. AWS CLI Setup (Daily Tool)

☐ Install AWS CLI v2
☐ Configure profile:

```bash
aws configure
```

☐ Verify identity:

```bash
aws sts get-caller-identity
```

☐ Create multiple CLI profiles:

* dev
* prod (even if fake)

☐ Understand:

* ~/.aws/credentials
* ~/.aws/config

📌 You should never hardcode keys

---

## 🗺️ E. Regions & AZ Practice

☐ List all regions:

```bash
aws ec2 describe-regions
```

☐ Set default region

```bash
export AWS_DEFAULT_REGION=ap-southeast-2
```

☐ Launch resources in different regions
☐ Observe they are **not shared**

📌 Explain:

> “Why ECR image in one region isn’t visible in another”

---

## 🧱 F. Resource Naming & Tagging (Professional Habit)

☐ Learn tagging best practices:

* Name
* Environment
* Owner
* Project
* CostCenter

☐ Apply tags to **everything**

* EC2
* VPC
* ECR
* EKS

☐ Use tags to filter in console

📌 Tags = visibility + cost control

---

## 🔑 G. Authentication vs Authorization

☐ Understand:

* Authentication = who you are
* Authorization = what you can do

☐ Test:

* User without permission → AccessDenied
* Same user after policy attach → works

☐ Use IAM Policy Simulator

📌 You should recognize AccessDenied instantly

---

## 🔐 H. Secrets & Environment Variables

☐ Understand why `.env` files are dangerous
☐ Learn environment variables usage:

```bash
export DB_HOST=localhost
```

☐ Store secrets in:

* Kubernetes secrets (later)
* AWS Secrets Manager (intro only)

☐ NEVER commit secrets to Git

📌 This prevents 80% of real-world breaches

---

## 📦 I. Core AWS Services Awareness (No Deep Dive Yet)

☐ Know **what problem each solves**:

* EC2
* S3
* IAM
* VPC
* RDS
* ECR
* CloudWatch

☐ Don’t memorize configs — understand purpose

📌 You should know:

> “Which service to Google when a problem appears”

---

## 🔥 J. CLI Survival Commands (Must Know)

☐ Check who you are:

```bash
aws sts get-caller-identity
```

☐ List S3 buckets:

```bash
aws s3 ls
```

☐ Describe EC2:

```bash
aws ec2 describe-instances
```

☐ Delete unused resources safely

---

## 💣 K. Break & Recover (Safe Learning)

☐ Delete a test IAM user → recreate
☐ Break permissions → fix AccessDenied
☐ Remove CLI config → reconfigure
☐ Rotate access keys

📌 Comfort comes from recovery, not success

---

## 🧪 L. Mini Capstone (Phase 0 Completion)

☐ Create IAM user (least privilege)
☐ Login via console
☐ Configure CLI
☐ Create S3 bucket
☐ Upload file via CLI
☐ Delete bucket

📌 If you can do this blindfolded → you’re ready

---

## 🎯 Phase 0 Exit Criteria

✅ You feel safe using AWS
✅ You understand IAM deeply
✅ CLI feels natural
✅ Billing anxiety is gone
✅ You can explain AWS to a beginner

---

## 🔜 What Comes After Phase 0?

👉 **Phase 1: Networking (VPC, NAT, IGW)**
👉 **Phase 2: Compute (EC2, ASG, ALB)**
👉 **Phase 3: Containers (ECR, ECS, EKS)**

If you want, I can:

* Convert this into a **daily schedule**
* Add **Terraform tasks**
* Map Phase 0 → AWS SAA exam objectives
* Build **hands-on challenges**

Just say the word 🚀
