# 📚 Phase 0 Complete Tutorial: AWS Fundamentals Mastery

**Tutorial Duration:** 7 days
**Last Updated:** January 2026
**Prerequisites:** Computer with internet, credit card for AWS account
**Goal:** Master AWS fundamentals through hands-on practice

---

## 📖 How to Use This Tutorial

This tutorial is designed to teach you AWS fundamentals through **practical exercises**. Each section contains:

1. **📝 Concept Explanation** - Theory and best practices
2. **🎯 Learning Objectives** - What you'll master
3. **💡 Practical Exercise** - Hands-on implementation
4. **✅ Verification Steps** - How to confirm success
5. **🤔 Knowledge Check** - Test your understanding
6. **⚠️ Common Mistakes** - What to avoid

**Study Approach:**
- Read the concept explanation first
- Complete the practical exercise
- Verify your work
- Answer knowledge check questions
- Move to next section only after mastering current one

---

# Table of Contents

1. [AWS Account Setup & Root Security](#day-1-aws-account-setup--root-security)
2. [IAM Fundamentals](#day-2-iam-fundamentals)
3. [AWS CLI Mastery](#day-2-3-aws-cli-mastery)
4. [Regions, AZs & Global Infrastructure](#day-3-regions-azs--global-infrastructure)
5. [S3 Deep Dive](#day-3-4-s3-deep-dive)
6. [IAM Policies & Access Control](#day-4-5-iam-policies--access-control)
7. [Billing & Cost Management](#day-6-billing--cost-management)
8. [Security Best Practices](#day-6-7-security-best-practices)
9. [Troubleshooting & Recovery](#day-7-troubleshooting--recovery)
10. [Final Capstone Project](#day-7-final-capstone-project)

---

# Day 1: AWS Account Setup & Root Security

## 📝 Concept: AWS Account Root User

### What is the Root User?

When you create an AWS account, you create a **root user** - the most powerful identity in your AWS account with unrestricted access to all resources and billing information.

**Key Characteristics:**
- Has **complete access** to all AWS services
- Can change billing information and close the account
- Cannot be restricted by IAM policies
- Uses email address as username
- Should **NEVER** be used for daily operations

### Why Root Security Matters

Think of the root user as the master key to your house. You wouldn't carry it everywhere - you'd lock it in a safe and use regular keys for daily access.

**Risks of compromised root account:**
- Attacker can delete ALL resources
- Can rack up massive bills ($10k+ in hours)
- Can steal data or create backdoors
- Nearly impossible to recover quickly

### AWS Account Structure

```
┌─────────────────────────────────────┐
│     AWS Account (Root User)         │
│  Email: your-email@example.com      │
│  Password: [Strong + MFA]           │
│  Access: EVERYTHING                 │
└─────────────────────────────────────┘
           │
           ├─── Billing & Cost Management
           ├─── IAM Users (alice, bob, charlie)
           ├─── IAM Roles
           ├─── All AWS Resources
           └─── Account Settings
```

---

## 🎯 Learning Objectives

By the end of this section, you will:

- ✅ Create and secure an AWS account
- ✅ Enable MFA (Multi-Factor Authentication) on root
- ✅ Understand the principle of least privilege
- ✅ Set up billing alerts to prevent surprise charges
- ✅ Create an IAM admin user for daily operations

---

## 💡 Practical Exercise 1.1: Create AWS Account

### Step 1: Sign Up for AWS

1. Go to https://aws.amazon.com
2. Click **Create an AWS Account**
3. Provide:
   - Email address (use one you control)
   - Password (minimum 8 characters, mix of upper/lower/numbers/symbols)
   - AWS account name (e.g., "MyLearningAccount")

**🔐 Password Best Practice:**
```
❌ Bad: password123
❌ Bad: MyAWSAccount2024
✅ Good: mK9$pL2#vN8@qR4 (use password manager!)
```

4. Select **Personal** account type
5. Provide contact information
6. Enter payment information (credit/debit card)
7. Verify phone number
8. Select **Basic Support Plan** (free)

**💰 Billing Note:** AWS won't charge you unless you exceed free tier limits. We'll set up alerts next.

### Step 2: Initial Login

1. Go to https://console.aws.amazon.com
2. Choose **Root user**
3. Enter your email address
4. Enter password
5. Complete any additional verification

---

## 💡 Practical Exercise 1.2: Enable MFA on Root

### What is MFA?

Multi-Factor Authentication requires **two things** to log in:
1. **Something you know** (password)
2. **Something you have** (phone with authenticator app)

Even if someone steals your password, they can't access your account without your phone.

### Step-by-Step MFA Setup

**Prerequisites:**
- Smartphone
- Authenticator app (Google Authenticator, Authy, or 1Password)

**Steps:**

1. **Navigate to IAM Dashboard**
   - In AWS Console, search for "IAM" in the top search bar
   - Click **IAM** service

2. **Configure MFA for Root**
   - On the right side, you'll see a security alert box
   - Click **Add MFA** next to "Activate MFA on your root account"

   **Alternative path:**
   - Click your account name (top right)
   - Select **Security credentials**
   - Scroll to **Multi-factor authentication (MFA)**
   - Click **Assign MFA device**

3. **Choose MFA Device Type**
   - Select **Authenticator app** (recommended)
   - Click **Continue**

4. **Set Up Authenticator App**
   - Open your authenticator app on phone
   - Click **Scan QR code** or **Add account**
   - Scan the QR code shown in AWS Console

   **If QR scan doesn't work:**
   - Click "Show secret key"
   - Manually enter the key in your app

5. **Enter Two Consecutive MFA Codes**
   - Your app generates a 6-digit code every 30 seconds
   - Enter the current code in **MFA code 1**
   - Wait for code to refresh (~30 seconds)
   - Enter the new code in **MFA code 2**
   - Click **Add MFA**

6. **Verify Success**
   - You should see "Successfully assigned MFA"
   - Your MFA device should show as **Active**

**🎯 Test MFA:**

1. Sign out of AWS Console
2. Sign back in as root user
3. After entering password, you'll be prompted for MFA code
4. Enter the 6-digit code from your authenticator app
5. Success!

---

## 💡 Practical Exercise 1.3: Set Up Billing Alerts

### Why Multiple Billing Alerts?

AWS charges can accumulate quickly. Multiple alerts act as an early warning system:

- **$5 alert** - "Something's running, investigate"
- **$10 alert** - "Urgent, shut things down"
- **Free tier alert** - "About to exceed free tier"

### Step 1: Enable Billing Preferences

1. **Navigate to Billing Dashboard**
   - Click your account name (top right)
   - Select **Billing and Cost Management**

2. **Enable Cost Alerts**
   - In left sidebar, click **Billing preferences**
   - Check these boxes:
     - ✅ **Receive AWS Free Tier alerts**
     - ✅ **Receive CloudWatch billing alerts**
   - Enter your email address
   - Click **Save preferences**

3. **Verify Email**
   - Check your inbox
   - Click confirmation link in AWS email

### Step 2: Create $5 Billing Alert

1. **Navigate to CloudWatch**
   - In top search bar, type "CloudWatch"
   - Click **CloudWatch** service
   - **Important:** Make sure you're in **us-east-1** region (top right)
   - Billing metrics only exist in us-east-1!

2. **Create Alarm**
   - In left sidebar, click **Alarms** → **All alarms**
   - Click **Create alarm**

3. **Select Metric**
   - Click **Select metric**
   - Click **Billing** → **Total Estimated Charge**
   - Check the box next to **USD**
   - Click **Select metric**

4. **Configure Conditions**
   - Under **Threshold type**, select **Static**
   - Under **Whenever EstimatedCharges is...**, select **Greater**
   - Enter threshold value: **5**
   - Click **Next**

5. **Configure Action**
   - Under **Alarm state trigger**, select **In alarm**
   - Click **Create new topic**
   - Topic name: `billing-alerts`
   - Email endpoint: your email address
   - Click **Create topic**
   - Click **Next**

6. **Name and Create Alarm**
   - Alarm name: `Billing-Alert-$5`
   - Alarm description: `Alert when charges exceed $5`
   - Click **Next**
   - Review settings
   - Click **Create alarm**

7. **Confirm SNS Subscription**
   - Check your email
   - Click **Confirm subscription** in the AWS notification email
   - You should see "Subscription confirmed!"

### Step 3: Create $10 Billing Alert

Repeat Step 2, but change:
- Threshold value: **10**
- Alarm name: `Billing-Alert-$10`
- Use existing SNS topic: `billing-alerts` (don't create new one)

### Step 4: Enable Cost Explorer

1. In **Billing and Cost Management** dashboard
2. Click **Cost Explorer** in left sidebar
3. Click **Enable Cost Explorer**
4. Wait 24 hours for data to populate

---

## 💡 Practical Exercise 1.4: Create IAM Admin User

### Why Not Use Root?

**Root user risks:**
- No restrictions - can accidentally delete everything
- Can't be logged or audited properly
- If credentials leak, entire account is compromised
- No way to implement least privilege

**IAM user benefits:**
- Can assign specific permissions
- Can be disabled without affecting account
- All actions are logged in CloudTrail
- Can enforce MFA, password policies
- Can have multiple users for team

### Step-by-Step IAM Admin Creation

1. **Navigate to IAM**
   - Search for "IAM" in top search bar
   - Click **IAM** service

2. **Create User**
   - In left sidebar, click **Users**
   - Click **Add users** (or **Create user**)
   - User name: `admin-yourname` (replace with your name, e.g., `admin-abhishant`)

3. **Configure Access**
   - Check **both** boxes:
     - ✅ **Provide user access to the AWS Management Console**
     - ✅ **I want to create an IAM user** (if prompted)
   - Console password: Choose **Custom password**
   - Enter a strong password
   - **Uncheck** "Users must create a new password at next sign-in" (we'll enable this later)
   - Click **Next**

4. **Set Permissions**
   - Select **Attach policies directly**
   - Search for: `AdministratorAccess`
   - Check the box next to **AdministratorAccess**
   - Click **Next**

5. **Review and Create**
   - Review the user details
   - Click **Create user**

6. **Save Login Details**
   - **Critical:** Save these details!
   - Console sign-in URL (e.g., `https://123456789012.signin.aws.amazon.com/console`)
   - Username: `admin-yourname`
   - Password: [your password]

   **💾 Save to password manager immediately!**

7. **Test Admin Login**
   - **Open incognito/private browser window** (don't sign out of root yet)
   - Go to console sign-in URL
   - Enter IAM user credentials
   - You should land on AWS Console

### Create Access Keys for CLI

1. **In IAM Console** (as root or admin user)
2. Click **Users** → `admin-yourname`
3. Click **Security credentials** tab
4. Scroll to **Access keys**
5. Click **Create access key**
6. Select use case: **Command Line Interface (CLI)**
7. Check "I understand the above recommendation"
8. Click **Next**
9. Add description tag (optional): "My laptop CLI access"
10. Click **Create access key**

**💾 CRITICAL - Save These:**
```
Access key ID: AKIAIOSFODNN7EXAMPLE
Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

**⚠️ The secret key is shown ONLY ONCE. Save it NOW to:**
- Password manager
- Download CSV file
- Secure note on your device

11. Click **Done**

### Enable MFA for Admin User

1. Still in **Security credentials** tab for your admin user
2. Scroll to **Multi-factor authentication (MFA)**
3. Click **Assign MFA device**
4. Device name: `admin-yourname-mfa`
5. Select **Authenticator app**
6. Click **Next**
7. Scan QR code with authenticator app (use same app as root MFA)
8. Enter two consecutive codes
9. Click **Add MFA**

---

## ✅ Verification Steps

### Checklist:

- [ ] AWS account created successfully
- [ ] Root user MFA enabled (test login with MFA code)
- [ ] Billing alerts configured for $5 and $10
- [ ] Billing alert email subscriptions confirmed
- [ ] Cost Explorer enabled
- [ ] IAM admin user created with AdministratorAccess
- [ ] Admin user MFA enabled
- [ ] Admin user access keys created and saved
- [ ] Admin console login tested successfully
- [ ] Root user credentials saved in password manager

### Test Your Setup:

**Test 1: Root Login with MFA**
```bash
# In incognito window
1. Go to https://console.aws.amazon.com
2. Select "Root user"
3. Enter root email
4. Enter root password
5. Enter MFA code
6. Should land on console
✅ Success!
```

**Test 2: Admin User Login**
```bash
# In different incognito window
1. Go to your IAM user sign-in URL
2. Enter admin username
3. Enter admin password
4. Enter MFA code
5. Should land on console
✅ Success!
```

**Test 3: Verify Billing Alerts**
```bash
1. Go to CloudWatch → Alarms
2. Should see:
   - Billing-Alert-$5 (State: OK)
   - Billing-Alert-$10 (State: OK)
✅ Success!
```

---

## 🤔 Knowledge Check

Answer these questions **without** looking back:

### Question 1: Why MFA on Root?
<details>
<summary>Click to reveal answer</summary>

**Answer:** Root user has unlimited access to everything. If someone steals just your password, they can't access the account without also having your phone with the MFA app. MFA adds a physical device requirement, protecting against password breaches, phishing, and keyloggers.

</details>

### Question 2: What's wrong with this setup?
```
User: developer-alice
Attached Policies: AdministratorAccess
Daily tasks: Deploy code, check logs
```

<details>
<summary>Click to reveal answer</summary>

**Answer:** Violates **principle of least privilege**. Alice has full admin access but only needs permissions to deploy code and read logs. If her credentials are compromised, attacker has full account access. She should only have permissions for EC2, S3, CloudWatch (not AdministratorAccess).

</details>

### Question 3: Billing Alert Logic
You set alerts at $5 and $10. Today you receive the $5 alert. What should you do?

<details>
<summary>Click to reveal answer</summary>

**Answer:**
1. **Immediately investigate** - Go to Cost Explorer, check what services are charging
2. **Identify the resource** - Is it expected (your project) or unexpected (compromised account)?
3. **Take action**:
   - Expected: Monitor closely, ensure it stays within budget
   - Unexpected: **Immediately** stop/delete the resources
4. **Don't wait for $10 alert** - By then you might already have a bigger bill

</details>

### Question 4: Lost Access Keys
You saved your admin user's access key ID but forgot the secret access key. Can you retrieve it?

<details>
<summary>Click to reveal answer</summary>

**Answer:** **No**, you cannot retrieve the secret access key after creation. AWS never stores or displays it again for security reasons.

**Solution:**
1. Delete the old access key (in IAM → Users → Security credentials)
2. Create a new access key pair
3. Save the new secret access key immediately

**Lesson:** Always save both access key ID and secret access key in a secure password manager immediately upon creation.

</details>

### Question 5: Root vs IAM Admin
What can root user do that IAM admin user (with AdministratorAccess) cannot?

<details>
<summary>Click to reveal answer</summary>

**Answer:** Root user can:
1. **Close the AWS account**
2. **Change account email address**
3. **Modify account name**
4. **Change support plan**
5. **Restore IAM permissions if you lock yourself out**
6. **Enable MFA on root (IAM admin can't change root's MFA)**
7. **Access certain billing tasks** (change billing contacts, enable IAM access to billing)

Even AdministratorAccess policy doesn't grant these account-level permissions. This is why root must be protected differently.

</details>

---

## ⚠️ Common Mistakes

### Mistake 1: Using Root for Daily Work
```bash
❌ WRONG:
- Using root to create EC2 instances
- Root access key stored in ~/.aws/credentials
- Sharing root password with team members

✅ RIGHT:
- Root locked away, only used for account-level tasks
- IAM users for all daily operations
- Each team member has their own IAM user
```

### Mistake 2: Weak Passwords
```bash
❌ WRONG:
Root password: MyAWS2024
Admin password: admin123

✅ RIGHT:
Root password: mK9$pL2#vN8@qR4!tY6
Admin password: pZ4#kM8@xN2!rQ5$vL9
(Generated by password manager)
```

### Mistake 3: Ignoring Billing Alerts
```bash
❌ WRONG:
"I'll check it tomorrow" (bill now $100)
"It's probably fine" (bill now $1,000)

✅ RIGHT:
Alert received → Investigate within 10 minutes
Unexpected charge → Stop resources immediately
```

### Mistake 4: Not Saving Access Keys
```bash
❌ WRONG:
- Click "Done" without downloading CSV
- Save to unsecured text file on desktop
- Email keys to yourself

✅ RIGHT:
- Download CSV immediately
- Store in password manager (1Password, LastPass, Bitwarden)
- Delete downloaded CSV after importing to password manager
```

### Mistake 5: Wrong Region for Billing
```bash
❌ WRONG:
Creating billing alarms in eu-west-1

✅ RIGHT:
Billing alarms MUST be created in us-east-1
(Billing metrics only exist in that region)
```

---

## 📊 Day 1 Summary

### What You Learned:

- ✅ AWS account structure and root user concept
- ✅ Multi-factor authentication (MFA) importance
- ✅ Billing alerts and cost monitoring
- ✅ IAM admin user creation
- ✅ Access keys vs console access
- ✅ Principle of least privilege

### Resources Created:

| Resource | Purpose | Cost |
|----------|---------|------|
| Root user MFA | Secure root account | Free |
| IAM admin user | Daily operations | Free |
| CloudWatch billing alarms (2) | Cost monitoring | Free |
| IAM access keys | CLI access | Free |

**Total Day 1 Cost: $0.00**

### Tomorrow: IAM Deep Dive

Tomorrow you'll learn:
- IAM users vs groups vs roles
- Creating custom policies
- Policy evaluation logic
- Testing permissions with Policy Simulator

---

# Day 2: IAM Fundamentals

## 📝 Concept: Identity and Access Management (IAM)

### What is IAM?

IAM is AWS's permission system. It answers two critical questions:

1. **Who are you?** (Authentication)
2. **What can you do?** (Authorization)

Think of IAM like a building security system:
- **Users** = Employees with badge IDs
- **Groups** = Departments (Engineering, HR, Finance)
- **Roles** = Temporary visitor badges
- **Policies** = Rules about which rooms each badge can access

### IAM Components

```
┌─────────────────────────────────────────┐
│              IAM Components              │
├─────────────────────────────────────────┤
│                                          │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐ │
│  │  Users  │  │ Groups  │  │  Roles  │ │
│  └────┬────┘  └────┬────┘  └────┬────┘ │
│       │            │             │      │
│       └────────────┴─────────────┘      │
│                    │                    │
│              ┌─────▼─────┐              │
│              │ Policies  │              │
│              │ (JSON)    │              │
│              └───────────┘              │
│                                          │
└─────────────────────────────────────────┘
```

### IAM Users

**IAM User** = A permanent, named identity with long-term credentials

**When to use:**
- Specific person needs access (Alice, Bob, Charlie)
- Application needs access (rare, prefer roles)
- Long-term access required

**Characteristics:**
- Has a username
- Has a password (for console) OR access keys (for CLI/API) OR both
- Exists until you delete it
- Can be a member of multiple groups
- Can have policies attached directly (but shouldn't)

**Real-world example:**
```
User: alice-developer
Purpose: Alice needs daily access to deploy applications
Credentials:
  - Console password (for AWS Console)
  - Access keys (for CLI from her laptop)
Groups: developers, deployers
```

### IAM Groups

**IAM Group** = A collection of users with similar permissions

**When to use:**
- Multiple users need the same permissions
- Want to manage permissions at scale
- Following least privilege by role/function

**Characteristics:**
- Just a container for users
- Cannot log in (not an identity)
- Policies attached to group apply to all members
- Users can be in multiple groups (max 10)
- Groups cannot contain other groups (no nesting)

**Real-world example:**
```
Group: developers
Members: alice-dev, bob-dev, charlie-dev
Policies: S3 read/write, EC2 read-only, CloudWatch logs
Purpose: All developers get same baseline permissions
```

### IAM Roles

**IAM Role** = A temporary identity that anyone/anything can "assume"

**When to use:**
- AWS services need to access other services (EC2 accessing S3)
- Cross-account access (Account A user accessing Account B)
- Temporary access (contractor needs access for 2 weeks)
- Federation (employees log in with Google/Microsoft accounts)

**Characteristics:**
- No permanent credentials (no password/keys)
- Credentials are temporary (15 min to 12 hours)
- Anyone/anything can assume if trusted
- Defined by two policies:
  1. **Trust policy** - Who can assume this role?
  2. **Permission policy** - What can role do?

**Real-world example:**
```
Role: EC2-S3-ReadOnly-Role
Trust policy: EC2 service can assume
Permission policy: Can read from specific S3 buckets
Use case: EC2 instances read config files from S3
Why role?: No need to store access keys on EC2
```

### IAM Policies

**IAM Policy** = A JSON document that defines permissions

**Policy structure:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

**Policy components:**
- **Effect**: `Allow` or `Deny`
- **Action**: What API operations? (e.g., `s3:PutObject`, `ec2:StartInstances`)
- **Resource**: Which AWS resources? (specified by ARN)
- **Condition**: When does this apply? (optional)

**Policy types:**

1. **Identity-based** - Attached to users/groups/roles
2. **Resource-based** - Attached to resources (S3 buckets, SQS queues)
3. **AWS managed** - Pre-built by AWS (e.g., AdministratorAccess)
4. **Customer managed** - Custom policies you create
5. **Inline** - Embedded directly in user/group/role (avoid these)

---

## 🎯 Learning Objectives

By the end of this section, you will:

- ✅ Create IAM users, groups, and roles
- ✅ Write custom IAM policies from scratch
- ✅ Understand policy evaluation logic
- ✅ Test permissions with Policy Simulator
- ✅ Implement least privilege access
- ✅ Debug permission issues

---

## 💡 Practical Exercise 2.1: Create IAM Groups

### Scenario

You're building the Document Vault project. You need three types of users:
1. **Admins** - Full access to everything
2. **Developers** - Read/write to dev buckets
3. **Readonly** - Can only view/download files

### Step 1: Create Admin Group

1. **Navigate to IAM**
   - Sign in as your admin user (not root!)
   - Search for "IAM" → Click IAM service

2. **Create Group**
   - Left sidebar → **User groups**
   - Click **Create group**
   - Group name: `admins`
   - **Don't attach policies yet** (we'll create custom ones)
   - Click **Create group**

### Step 2: Create Developer Group

1. Click **Create group**
2. Group name: `developers`
3. Click **Create group**

### Step 3: Create Readonly Group

1. Click **Create group**
2. Group name: `readonly`
3. Click **Create group**

### Verify

```bash
# Using AWS CLI (we'll set this up soon)
aws iam list-groups

# Expected output:
{
  "Groups": [
    {
      "Path": "/",
      "GroupName": "admins",
      "GroupId": "AGPAI...",
      "Arn": "arn:aws:iam::123456789012:group/admins",
      "CreateDate": "2026-01-06T..."
    },
    {
      "GroupName": "developers",
      ...
    },
    {
      "GroupName": "readonly",
      ...
    }
  ]
}
```

---

## 💡 Practical Exercise 2.2: Create Custom IAM Policies

### Understanding Policy Structure

Let's break down a simple policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ListBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::my-bucket"
    },
    {
      "Sid": "AllowS3GetObject",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

**Breakdown:**
- `Version`: Always use "2012-10-17" (current policy language version)
- `Statement`: Array of permission statements
- `Sid`: Statement ID (optional, for documentation)
- `Effect`: "Allow" or "Deny"
- `Action`: AWS API action (service:Action format)
- `Resource`: ARN of the resource

**ARN format:**
```
arn:partition:service:region:account-id:resource-type/resource-id

Example:
arn:aws:s3:::my-bucket/*
└─┘ └─┘ └┘ └──────────┘
 │   │  │       │
 │   │  │       └─ Resource (all objects in bucket)
 │   │  └───────── Region (S3 is global, so empty)
 │   └──────────── Service (S3)
 └──────────────── Partition (aws, aws-cn, aws-us-gov)
```

### Step 1: Create Admin Policy

**Policy: DocumentVaultAdminPolicy**
- Full access to all S3 buckets with "docvault" prefix
- Full access to IAM read operations
- Access to CloudWatch logs

1. **Navigate to IAM → Policies**
   - Left sidebar → **Policies**
   - Click **Create policy**

2. **Switch to JSON editor**
   - Click **JSON** tab
   - Replace existing JSON with:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3FullAccessToDocVault",
      "Effect": "Allow",
      "Action": [
        "s3:*"
      ],
      "Resource": [
        "arn:aws:s3:::docvault-*",
        "arn:aws:s3:::docvault-*/*"
      ]
    },
    {
      "Sid": "AllowListAllBuckets",
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets",
        "s3:GetBucketLocation"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowIAMReadAccess",
      "Effect": "Allow",
      "Action": [
        "iam:Get*",
        "iam:List*"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowCloudWatchLogs",
      "Effect": "Allow",
      "Action": [
        "logs:*"
      ],
      "Resource": "*"
    }
  ]
}
```

3. **Review and create**
   - Click **Next**
   - Policy name: `DocumentVaultAdminPolicy`
   - Description: `Full access to Document Vault S3 buckets and related services`
   - Click **Create policy**

### Step 2: Create Developer Policy

**Policy: DocumentVaultDevPolicy**
- Read/write to S3 buckets
- Cannot delete buckets
- Can read IAM info (to see own permissions)

1. Click **Create policy** → **JSON** tab
2. Enter this policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadWrite",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": [
        "arn:aws:s3:::docvault-*",
        "arn:aws:s3:::docvault-*/*"
      ]
    },
    {
      "Sid": "AllowListAllBuckets",
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowIAMReadOwnUser",
      "Effect": "Allow",
      "Action": [
        "iam:GetUser",
        "iam:ListAccessKeys"
      ],
      "Resource": "arn:aws:iam::*:user/${aws:username}"
    }
  ]
}
```

**Special variable:** `${aws:username}` gets replaced with the user's actual username. This lets users see their own info but not others'.

3. Click **Next**
4. Policy name: `DocumentVaultDevPolicy`
5. Description: `Read/write access to Document Vault S3 buckets`
6. Click **Create policy**

### Step 3: Create Readonly Policy

**Policy: DocumentVaultReadPolicy**
- Can only list and download files
- Cannot upload, delete, or modify

1. Click **Create policy** → **JSON** tab
2. Enter:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadOnly",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": [
        "arn:aws:s3:::docvault-*",
        "arn:aws:s3:::docvault-*/*"
      ]
    },
    {
      "Sid": "AllowListAllBuckets",
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets"
      ],
      "Resource": "*"
    }
  ]
}
```

3. Click **Next**
4. Policy name: `DocumentVaultReadPolicy`
5. Description: `Read-only access to Document Vault S3 buckets`
6. Click **Create policy**

---

## 💡 Practical Exercise 2.3: Attach Policies to Groups

Now let's connect our policies to groups.

### Step 1: Attach Policy to Admins Group

1. **Navigate to User Groups**
   - Left sidebar → **User groups**
   - Click **admins** group

2. **Attach policy**
   - Click **Permissions** tab
   - Click **Add permissions** → **Attach policies**
   - Search for: `DocumentVaultAdminPolicy`
   - Check the box
   - Click **Attach policies**

### Step 2: Attach Policy to Developers Group

1. Click **User groups** → **developers**
2. Click **Permissions** tab → **Add permissions** → **Attach policies**
3. Search for: `DocumentVaultDevPolicy`
4. Check the box
5. Click **Attach policies**

### Step 3: Attach Policy to Readonly Group

1. Click **User groups** → **readonly**
2. Click **Permissions** tab → **Add permissions** → **Attach policies**
3. Search for: `DocumentVaultReadPolicy`
4. Check the box
5. Click **Attach policies**

---

## 💡 Practical Exercise 2.4: Create IAM Users

### Step 1: Create Alice (Admin User)

1. **Navigate to Users**
   - Left sidebar → **Users**
   - Click **Add users** (or **Create user**)

2. **User details**
   - User name: `alice-admin`
   - Check: **Provide user access to AWS Management Console**
   - Select: **I want to create an IAM user**
   - Console password: **Custom password** → enter: `TempPass123!@#`
   - Check: **Users must create a new password at next sign-in**
   - Click **Next**

3. **Set permissions**
   - Select: **Add user to group**
   - Check: **admins** group
   - Click **Next**

4. **Review and create**
   - Review details
   - Click **Create user**

5. **Save login details**
   - Save console sign-in URL
   - Username: `alice-admin`
   - Temporary password: `TempPass123!@#`

### Step 2: Create Bob (Developer User)

1. Click **Add users**
2. User name: `bob-dev`
3. Enable console access with custom password: `TempPass456!@#`
4. Require password change at next sign-in
5. Add to group: **developers**
6. Create user

### Step 3: Create Charlie (Readonly User)

1. Click **Add users**
2. User name: `charlie-readonly`
3. Enable console access with custom password: `TempPass789!@#`
4. Require password change at next sign-in
5. Add to group: **readonly**
6. Create user

---

## 💡 Practical Exercise 2.5: Create IAM Role for EC2

### What We're Building

We'll create a role that allows EC2 instances to read from S3 buckets. This is a common pattern - instead of storing access keys on the EC2 instance, we assign a role.

### Step 1: Create Role

1. **Navigate to Roles**
   - Left sidebar → **Roles**
   - Click **Create role**

2. **Select trusted entity**
   - Trusted entity type: **AWS service**
   - Use case: **EC2**
   - Click **Next**

3. **Add permissions**
   - Search for: `DocumentVaultReadPolicy`
   - Check the box
   - Click **Next**

4. **Name and create**
   - Role name: `EC2-DocumentVault-ReadOnly-Role`
   - Description: `Allows EC2 instances to read from Document Vault S3 buckets`
   - Click **Create role**

### Step 2: Examine Trust Policy

1. Click on the newly created role
2. Click **Trust relationships** tab
3. You'll see:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**Translation:** "Any EC2 instance can assume this role" (via sts:AssumeRole action)

**Principal** = Who can assume the role
- `"Service": "ec2.amazonaws.com"` = EC2 service
- Could also be: `"AWS": "arn:aws:iam::123456:user/alice"` (specific user)
- Or: `"AWS": "arn:aws:iam::ANOTHER-ACCOUNT:root"` (entire other AWS account)

---

## ✅ Verification Steps

### Test 1: Verify Groups and Policies

```bash
# List all groups
aws iam list-groups --profile admin

# Get policies attached to admins group
aws iam list-attached-group-policies --group-name admins --profile admin

# Expected output:
{
  "AttachedPolicies": [
    {
      "PolicyName": "DocumentVaultAdminPolicy",
      "PolicyArn": "arn:aws:iam::123456789012:policy/DocumentVaultAdminPolicy"
    }
  ]
}
```

### Test 2: Verify Users and Group Membership

```bash
# List users in admins group
aws iam get-group --group-name admins --profile admin

# Get groups for alice-admin user
aws iam list-groups-for-user --user-name alice-admin --profile admin

# Expected: alice-admin is in admins group
```

### Test 3: Verify Role

```bash
# Get role details
aws iam get-role --role-name EC2-DocumentVault-ReadOnly-Role --profile admin

# List attached policies
aws iam list-attached-role-policies --role-name EC2-DocumentVault-ReadOnly-Role --profile admin

# Expected: DocumentVaultReadPolicy attached
```

---

## 🤔 Knowledge Check

### Question 1: Groups vs Direct Attachment

What's wrong with this approach?

```bash
# Attach policy directly to each user
aws iam attach-user-policy --user-name alice --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
aws iam attach-user-policy --user-name bob --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
aws iam attach-user-policy --user-name charlie --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
```

<details>
<summary>Click to reveal answer</summary>

**Answer:**

**Problems:**
1. **Doesn't scale** - Imagine 100 developers, you'd attach policy 100 times
2. **Hard to manage** - To change permissions, you must update 100 users individually
3. **Error-prone** - Easy to forget to update one user
4. **No grouping logic** - Can't see "all developers" easily

**Better approach:**
```bash
# Create group once
aws iam create-group --group-name developers

# Attach policy to group once
aws iam attach-group-policy --group-name developers --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess

# Add users to group (repeatable, but organized)
aws iam add-user-to-group --user-name alice --group-name developers
aws iam add-user-to-group --user-name bob --group-name developers
aws iam add-user-to-group --user-name charlie --group-name developers

# To change permissions: Update group policy once, affects all members
```

**Benefits:**
- Change once, affect many
- Clear organization (who's a developer?)
- Easier auditing
- Follows AWS best practices

</details>

### Question 2: When to Use Roles vs Users?

**Scenario A:** You have an EC2 instance that needs to read from S3.
**Scenario B:** Your coworker Alice needs daily access to AWS.

Which should use a role, and which should use a user?

<details>
<summary>Click to reveal answer</summary>

**Answer:**

**Scenario A: EC2 → S3 = Use IAM Role**

**Why:**
- EC2 is a service, not a person
- No need for permanent credentials (access keys)
- Role provides temporary credentials automatically
- More secure (no keys stored on instance)
- Automatic credential rotation

**Implementation:**
```bash
# Create role
aws iam create-role --role-name EC2-S3-ReadOnly

# Attach to EC2 instance
aws ec2 associate-iam-instance-profile --instance-id i-1234567890abcdef0 --iam-instance-profile EC2-S3-ReadOnly
```

**Scenario B: Alice = Use IAM User**

**Why:**
- Alice is a specific person
- Needs long-term, consistent access
- Needs to log into console (requires username/password)
- Needs CLI access (requires access keys)
- Permissions tied to identity for auditing

**Implementation:**
```bash
# Create user
aws iam create-user --user-name alice

# Add to group
aws iam add-user-to-group --user-name alice --group-name developers
```

**Rule of thumb:**
- **Person** = User
- **AWS service** (EC2, Lambda, etc.) = Role
- **Temporary access** = Role
- **Cross-account access** = Role

</details>

### Question 3: Policy Evaluation Logic

Alice is in two groups:
- **developers** group has: `DocumentVaultDevPolicy` (allows s3:PutObject)
- **readonly** group has: `DocumentVaultReadPolicy` (does NOT allow s3:PutObject)

Can Alice upload files to S3?

<details>
<summary>Click to reveal answer</summary>

**Answer: YES, Alice can upload files.**

**AWS IAM Policy Evaluation Logic:**

```
1. By default, everything is DENIED
2. Explicit ALLOW grants permission
3. Explicit DENY overrides all allows
4. If no policy mentions an action, it's denied
```

**Alice's situation:**
- developers group policy has: `Allow s3:PutObject`
- readonly group policy doesn't mention `s3:PutObject` (implicit deny)
- **No explicit DENY exists**

**Evaluation:**
1. Start with: Default deny
2. Check all policies:
   - DocumentVaultDevPolicy says: **Allow s3:PutObject** ✅
   - DocumentVaultReadPolicy is silent on PutObject
3. Any Allow? **YES**
4. Any Deny? **NO**
5. **Result: ALLOWED**

**Important note:**
If there was an explicit Deny:
```json
{
  "Effect": "Deny",
  "Action": "s3:PutObject",
  "Resource": "*"
}
```
Then Alice would be blocked, even with the Allow in developers group. **Deny always wins.**

</details>

### Question 4: Resource-based vs Identity-based Policies

What's the difference between:

**Policy A (Identity-based):**
```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```
Attached to: IAM user Alice

**Policy B (Resource-based):**
```json
{
  "Effect": "Allow",
  "Principal": { "AWS": "arn:aws:iam::123456:user/alice" },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```
Attached to: S3 bucket `my-bucket`

<details>
<summary>Click to reveal answer</summary>

**Answer:**

**Policy A: Identity-based (attached to Alice)**
- Defines what Alice can do
- Lists actions and resources
- No Principal (it's implicit - the user it's attached to)
- Alice carries this permission with her everywhere
- **Perspective:** "Alice, you can get objects from my-bucket"

**Policy B: Resource-based (attached to bucket)**
- Defines who can access the bucket
- Lists principals (users/roles/accounts) who can access
- Specifies actions those principals can perform
- Bucket controls its own access
- **Perspective:** "Hey bucket, allow Alice to get your objects"

**When are resource-based policies useful?**

1. **Cross-account access:**
   ```json
   {
     "Principal": { "AWS": "arn:aws:iam::ANOTHER-ACCOUNT:root" },
     "Action": "s3:GetObject",
     "Resource": "arn:aws:s3:::my-bucket/*"
   }
   ```
   Bucket in Account A allows Account B users to access it.

2. **Service access:**
   ```json
   {
     "Principal": { "Service": "lambda.amazonaws.com" },
     "Action": "s3:GetObject",
     "Resource": "arn:aws:s3:::my-bucket/*"
   }
   ```
   Bucket allows Lambda service to read files.

3. **Public access (be careful!):**
   ```json
   {
     "Principal": "*",
     "Action": "s3:GetObject",
     "Resource": "arn:aws:s3:::public-website/*"
   }
   ```
   Anyone on internet can download files (for public website hosting).

**Key difference:**
- **Identity-based**: User says "I want to access bucket"
- **Resource-based**: Bucket says "I allow user to access me"

**Both must allow for access to work!** (Unless in same account, then one Allow is enough)

</details>

### Question 5: Debugging Permission Issues

Bob (developer) runs:
```bash
aws s3 cp file.txt s3://docvault-primary-myname-useast1/
```

Error:
```
An error occurred (AccessDenied) when calling the PutObject operation: Access Denied
```

Where do you look to debug this? (5 places to check)

<details>
<summary>Click to reveal answer</summary>

**Answer: 5 Places to Check**

**1. Check User's Groups**
```bash
aws iam list-groups-for-user --user-name bob-dev

# Is Bob in the correct group? (should be "developers")
```

**2. Check Group's Policies**
```bash
aws iam list-attached-group-policies --group-name developers

# Is DocumentVaultDevPolicy attached?
```

**3. Check Policy Contents**
```bash
aws iam get-policy --policy-arn arn:aws:iam::ACCOUNT:policy/DocumentVaultDevPolicy
aws iam get-policy-version --policy-arn arn:aws:iam::ACCOUNT:policy/DocumentVaultDevPolicy --version-id v1

# Does policy allow s3:PutObject?
# Does Resource ARN match the bucket name?
# Example issue: Policy says "arn:aws:s3:::docvault-*" but bucket is named "doc-vault-primary" (missing "vault")
```

**4. Check Bucket Policy (resource-based)**
```bash
aws s3api get-bucket-policy --bucket docvault-primary-myname-useast1

# Does bucket policy Deny anyone?
# Explicit Deny overrides Allow from IAM policy
```

**5. Check Bucket Public Access Block**
```bash
aws s3api get-public-access-block --bucket docvault-primary-myname-useast1

# If BlockPublicAcls is true and Bob's IP is considered "public", might block
```

**Common issues:**

**Issue 1: Typo in policy Resource**
```json
❌ "Resource": "arn:aws:s3:::docvault-primary-*/*"
✅ "Resource": "arn:aws:s3:::docvault-*/*"
```

**Issue 2: Missing /* for objects**
```json
❌ "Resource": "arn:aws:s3:::docvault-primary"       (just the bucket)
✅ "Resource": [
    "arn:aws:s3:::docvault-primary",          (for ListBucket)
    "arn:aws:s3:::docvault-primary/*"         (for objects)
  ]
```

**Issue 3: Wrong region/profile**
```bash
# Bob is using wrong AWS profile
echo $AWS_PROFILE
# Shows: admin  (should be: dev)

export AWS_PROFILE=dev
```

**Issue 4: Bucket doesn't exist**
```bash
aws s3 ls
# Is the bucket in the list? Typo in bucket name?
```

**Issue 5: Explicit Deny somewhere**
```json
# Someone added this to deny uploads on weekends:
{
  "Effect": "Deny",
  "Action": "s3:PutObject",
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "aws:RequestedRegion": "us-east-1",
      "aws:weekday": ["Saturday", "Sunday"]
    }
  }
}
```

**Debug tool: IAM Policy Simulator**
1. Go to IAM Console → Policy Simulator
2. Select user: bob-dev
3. Select service: S3
4. Select action: PutObject
5. Enter resource ARN: arn:aws:s3:::docvault-primary-myname-useast1/file.txt
6. Click **Run Simulation**
7. Shows: Allowed or Denied (with explanation!)

</details>

---

## ⚠️ Common Mistakes

### Mistake 1: Attaching Policies to Users (Not Groups)

```bash
❌ WRONG:
aws iam attach-user-policy --user-name alice --policy-arn ...
aws iam attach-user-policy --user-name bob --policy-arn ...
aws iam attach-user-policy --user-name charlie --policy-arn ...

✅ RIGHT:
aws iam create-group --group-name developers
aws iam attach-group-policy --group-name developers --policy-arn ...
aws iam add-user-to-group --user-name alice --group-name developers
aws iam add-user-to-group --user-name bob --group-name developers
```

**Why it matters:** Maintaining 100 individual user policies vs 1 group policy.

### Mistake 2: Overly Permissive Policies

```json
❌ WRONG:
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}

✅ RIGHT:
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject",
    "s3:PutObject"
  ],
  "Resource": "arn:aws:s3:::specific-bucket/*"
}
```

**Principle of least privilege:** Grant only the minimum permissions needed.

### Mistake 3: Forgetting Both Bucket and Object Permissions

```json
❌ WRONG (can't list bucket):
{
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}

✅ RIGHT:
{
  "Action": [
    "s3:ListBucket"              ← bucket-level action
  ],
  "Resource": "arn:aws:s3:::my-bucket"
},
{
  "Action": [
    "s3:GetObject",
    "s3:PutObject"               ← object-level actions
  ],
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

**S3 permissions have two levels:**
- **Bucket-level:** ListBucket, DeleteBucket (ARN: arn:aws:s3:::bucket-name)
- **Object-level:** GetObject, PutObject (ARN: arn:aws:s3:::bucket-name/*)

### Mistake 4: Hardcoding Account IDs

```json
❌ WRONG:
{
  "Resource": "arn:aws:iam::123456789012:user/alice"
}

✅ RIGHT:
{
  "Resource": "arn:aws:iam::*:user/${aws:username}"
}
```

Or use `*` for account ID:
```json
{
  "Resource": "arn:aws:iam::*:user/${aws:username}"
}
```

### Mistake 5: Using Inline Policies

```bash
❌ WRONG (inline policy):
aws iam put-user-policy --user-name alice --policy-name MyPolicy --policy-document file://policy.json

✅ RIGHT (managed policy):
aws iam create-policy --policy-name MyPolicy --policy-document file://policy.json
aws iam attach-user-policy --user-name alice --policy-arn arn:aws:iam::123:policy/MyPolicy
```

**Why avoid inline policies?**
- Not reusable
- Harder to audit (must check each user)
- No version control
- Can't see all places policy is used

---

## 📊 Day 2 Summary

### What You Learned:

- ✅ IAM users, groups, roles, policies
- ✅ Difference between identity-based and resource-based policies
- ✅ Creating custom IAM policies with JSON
- ✅ Policy evaluation logic (Allow vs Deny)
- ✅ Principle of least privilege
- ✅ Trust policies for roles

### Resources Created:

| Resource | Purpose | Cost |
|----------|---------|------|
| 3 IAM groups | Organize users by role | Free |
| 3 custom policies | Define permissions | Free |
| 3 IAM users | Alice, Bob, Charlie | Free |
| 1 IAM role | EC2 instance access | Free |

**Total Day 2 Cost: $0.00**

### Next: AWS CLI Setup

Tomorrow you'll:
- Install AWS CLI v2
- Configure multiple profiles
- Master CLI commands
- Use environment variables

---

# Day 2-3: AWS CLI Mastery

## 📝 Concept: AWS Command Line Interface (CLI)

### What is the AWS CLI?

The AWS CLI is a unified tool to manage AWS services from your terminal/command line. Instead of clicking through the console, you type commands.

**Why use CLI over Console?**

| Task | Console | CLI |
|------|---------|-----|
| Create 1 S3 bucket | 7 clicks | 1 command |
| Create 10 S3 buckets | 70 clicks | 1 command (loop) |
| Upload 100 files | 100 clicks | 1 command |
| Automate daily tasks | Impossible | Easy (scripts) |
| Audit (see what was run) | Hard | Easy (history) |
| Work from another computer | Hard | Easy (portable commands) |

**Real-world example:**
```bash
# Console: Click, click, click, type, click, click...
# CLI: One line
aws s3 mb s3://my-bucket --region us-east-1
```

### CLI Architecture

```
┌─────────────────────┐
│   Your Computer     │
│                     │
│  ┌──────────────┐   │
│  │ AWS CLI v2   │   │
│  └──────┬───────┘   │
│         │           │
└─────────┼───────────┘
          │
          │ HTTPS (API calls)
          │
┌─────────▼───────────┐
│   AWS Services      │
│                     │
│  ┌──────┐ ┌──────┐  │
│  │  S3  │ │ EC2  │  │
│  └──────┘ └──────┘  │
│  ┌──────┐ ┌──────┐  │
│  │ IAM  │ │ RDS  │  │
│  └──────┘ └──────┘  │
└─────────────────────┘
```

**How it works:**
1. You run: `aws s3 ls`
2. CLI reads credentials from `~/.aws/credentials`
3. CLI makes HTTPS API call to AWS
4. AWS verifies your identity (IAM)
5. AWS checks your permissions
6. AWS returns results
7. CLI displays results

### Authentication Methods

**1. Access Keys (programmatic access)**
```
Access Key ID: AKIAIOSFODNN7EXAMPLE
Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```
Used for: CLI, SDK, API calls

**2. Session Tokens (temporary)**
```
Access Key ID: ASIATESTACCESSKEYEXAMPLE
Secret Access Key: [secret]
Session Token: FwoGZXIvYXdz...
Valid for: 15 min - 12 hours
```
Used for: Assumed roles, federated access

**3. Console Password**
```
Username: alice-admin
Password: MyPassword123!
```
Used for: AWS Management Console only

### Configuration Files

**~/.aws/credentials** (stores secrets)
```ini
[default]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCY

[dev]
aws_access_key_id = AKIAI44QH8DHBEXAMPLE
aws_secret_access_key = je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEX

[prod]
aws_access_key_id = AKIAIOSFODNN8EXAMPLE
aws_secret_access_key = ARandomSecretKey
```

**~/.aws/config** (stores settings)
```ini
[default]
region = us-east-1
output = json

[profile dev]
region = us-west-2
output = json

[profile prod]
region = eu-west-1
output = table
```

**Why separate files?**
- `credentials` = secret (never commit to Git)
- `config` = settings (safe to commit)

---

## 🎯 Learning Objectives

By the end of this section, you will:

- ✅ Install AWS CLI v2
- ✅ Configure multiple profiles (admin, dev, prod)
- ✅ Switch between profiles effortlessly
- ✅ Use environment variables
- ✅ Understand credential precedence
- ✅ Master common CLI commands
- ✅ Debug CLI issues

---

## 💡 Practical Exercise 3.1: Install AWS CLI v2

### For macOS

```bash
# Download installer
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"

# Install
sudo installer -pkg AWSCLIV2.pkg -target /

# Verify
aws --version
# Expected output: aws-cli/2.x.x Python/3.x.x Darwin/...
```

### For Linux

```bash
# Download installer
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Unzip
unzip awscliv2.zip

# Install
sudo ./aws/install

# Verify
aws --version
```

### For Windows

1. Download: https://awscli.amazonaws.com/AWSCLIV2.msi
2. Run the installer
3. Open Command Prompt or PowerShell
4. Verify: `aws --version`

### Troubleshooting

**Issue: command not found: aws**
```bash
# Check if installed
which aws

# If not found, check PATH
echo $PATH

# AWS CLI default location: /usr/local/bin/aws
# Add to PATH in ~/.zshrc or ~/.bashrc:
export PATH="/usr/local/bin:$PATH"

# Reload shell
source ~/.zshrc  # or source ~/.bashrc
```

---

## 💡 Practical Exercise 3.2: Configure Admin Profile

### Step 1: Get Access Keys

We created access keys on Day 1. If you don't have them:

1. Sign in to AWS Console as admin user
2. Go to IAM → Users → [your admin user]
3. Click **Security credentials** tab
4. Click **Create access key**
5. Select **CLI**
6. Click **Next** → **Create access key**
7. **SAVE BOTH**: Access key ID and Secret access key

### Step 2: Configure Profile

```bash
aws configure --profile admin
```

You'll be prompted:

```
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCY
Default region name [None]: us-east-1
Default output format [None]: json
```

**What each setting means:**

- **Access Key ID**: Your username (sort of)
- **Secret Access Key**: Your password (never share!)
- **Region**: Where to create resources by default
- **Output format**: How to display results
  - `json` - structured data (best for scripting)
  - `table` - human-readable table
  - `text` - plain text (for simple parsing)
  - `yaml` - YAML format

### Step 3: Verify Configuration

```bash
# Check what profile you have
cat ~/.aws/credentials

# Should see:
[admin]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCY

# Check config
cat ~/.aws/config

# Should see:
[profile admin]
region = us-east-1
output = json
```

### Step 4: Test Profile

```bash
# Who am I?
aws sts get-caller-identity --profile admin

# Expected output:
{
  "UserId": "AIDAI23HXD2WQ4EXAMPLE",
  "Account": "123456789012",
  "Arn": "arn:aws:iam::123456789012:user/admin-yourname"
}
```

**What this command does:**
- `sts` = AWS Security Token Service
- `get-caller-identity` = Returns details about IAM identity making the request
- Shows: User ID, Account number, ARN

**Why this matters:**
- Confirms credentials work
- Shows which identity you're using
- Verifies you're in correct account

---

## 💡 Practical Exercise 3.3: Configure Multiple Profiles

### Create Dev Profile

```bash
aws configure --profile dev
```

Enter:
- Access Key ID: [Bob's access key from Day 2] (if you created access keys for Bob)
- Secret Access Key: [Bob's secret key]
- Region: `us-west-2` (different region for testing)
- Output: `json`

**If you didn't create access keys for Bob:**
1. Go to IAM → Users → bob-dev
2. Security credentials tab
3. Create access key → CLI → Create
4. Save credentials

### Create Prod Profile

```bash
aws configure --profile prod
```

Enter:
- Access Key ID: [Create another access key for admin user, or use same]
- Secret Access Key: [corresponding secret]
- Region: `eu-west-1` (Europe)
- Output: `table` (different format for visual distinction)

### Verify All Profiles

```bash
# List all profiles
cat ~/.aws/credentials

# Should see:
[admin]
aws_access_key_id = ...

[dev]
aws_access_key_id = ...

[prod]
aws_access_key_id = ...
```

### Test Each Profile

```bash
# Test admin profile
aws sts get-caller-identity --profile admin

# Test dev profile
aws sts get-caller-identity --profile dev

# Test prod profile
aws sts get-caller-identity --profile prod
```

Each should return different user ARNs or account info.

---

## 💡 Practical Exercise 3.4: Working with Profiles

### Method 1: --profile Flag (Recommended)

```bash
# Always specify profile explicitly
aws s3 ls --profile admin
aws ec2 describe-instances --profile dev
aws iam list-users --profile prod
```

**Pros:**
- Explicit (clear which profile)
- No accidents (won't use wrong profile)
- Works in scripts

**Cons:**
- Must type `--profile` every time

### Method 2: Environment Variable

```bash
# Set profile for current terminal session
export AWS_PROFILE=admin

# Now all commands use admin profile
aws s3 ls
aws iam list-users
aws sts get-caller-identity

# Switch to dev profile
export AWS_PROFILE=dev
aws sts get-caller-identity  # Now using dev credentials

# Unset (back to default)
unset AWS_PROFILE
```

**Pros:**
- Less typing
- Good for interactive sessions

**Cons:**
- Easy to forget which profile is active
- Can accidentally run commands with wrong profile

### Method 3: Default Profile

```bash
# Configure default profile (no --profile needed)
aws configure

# This creates [default] in credentials
# Now plain commands use default
aws s3 ls  # Uses default profile
```

**When to use:**
- Personal computer, only one AWS account
- Simple setups

**When NOT to use:**
- Multiple AWS accounts
- Shared computers
- Production environments

### Best Practice: Profile Aliases

Add to `~/.zshrc` or `~/.bashrc`:

```bash
# AWS Profile aliases
alias awsadmin='aws --profile admin'
alias awsdev='aws --profile dev'
alias awsprod='aws --profile prod'

# Now you can use:
awsadmin s3 ls
awsdev ec2 describe-instances
```

**Even better - show current profile in prompt:**

```bash
# Add to ~/.zshrc or ~/.bashrc
function aws_profile {
  if [ -n "$AWS_PROFILE" ]; then
    echo " [$AWS_PROFILE]"
  fi
}

# Update prompt to show profile
export PS1='$(aws_profile) $ '

# Now your terminal shows:
# [admin] $ aws s3 ls
# [dev] $ aws ec2 describe-instances
```

---

## 💡 Practical Exercise 3.5: Master Essential CLI Commands

### S3 Commands

```bash
# List all buckets
aws s3 ls --profile admin

# List contents of specific bucket
aws s3 ls s3://my-bucket/ --profile admin

# Upload file
aws s3 cp myfile.txt s3://my-bucket/ --profile admin

# Upload directory (recursive)
aws s3 cp myfolder/ s3://my-bucket/myfolder/ --recursive --profile admin

# Download file
aws s3 cp s3://my-bucket/myfile.txt ./ --profile admin

# Sync local directory with bucket (like rsync)
aws s3 sync ./local-folder s3://my-bucket/remote-folder/ --profile admin

# Delete file
aws s3 rm s3://my-bucket/myfile.txt --profile admin

# Delete all files in bucket
aws s3 rm s3://my-bucket/ --recursive --profile admin

# Create bucket
aws s3 mb s3://my-new-bucket --region us-east-1 --profile admin

# Delete bucket (must be empty first)
aws s3 rb s3://my-bucket --profile admin
```

### IAM Commands

```bash
# List all users
aws iam list-users --profile admin

# Get specific user details
aws iam get-user --user-name alice-admin --profile admin

# List groups for user
aws iam list-groups-for-user --user-name alice-admin --profile admin

# List policies attached to user
aws iam list-attached-user-policies --user-name alice-admin --profile admin

# List all groups
aws iam list-groups --profile admin

# Get group details
aws iam get-group --group-name developers --profile admin

# List policies attached to group
aws iam list-attached-group-policies --group-name developers --profile admin

# List all policies
aws iam list-policies --scope Local --profile admin  # Your custom policies
aws iam list-policies --scope AWS --profile admin    # AWS managed policies

# Get policy document
aws iam get-policy --policy-arn arn:aws:iam::123456:policy/MyPolicy --profile admin
aws iam get-policy-version --policy-arn arn:aws:iam::123456:policy/MyPolicy --version-id v1 --profile admin
```

### EC2 Commands

```bash
# List all regions
aws ec2 describe-regions --profile admin

# List all availability zones in current region
aws ec2 describe-availability-zones --profile admin

# List all EC2 instances
aws ec2 describe-instances --profile admin

# List instances with specific tag
aws ec2 describe-instances --filters "Name=tag:Environment,Values=production" --profile admin

# Start instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0 --profile admin

# Stop instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0 --profile admin
```

### Billing Commands

```bash
# Get cost and usage data
aws ce get-cost-and-usage \
  --time-period Start=2026-01-01,End=2026-01-07 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --profile admin

# Get cost forecast
aws ce get-cost-forecast \
  --time-period Start=2026-01-01,End=2026-01-31 \
  --metric UNBLENDED_COST \
  --granularity MONTHLY \
  --profile admin
```

---

## 💡 Practical Exercise 3.6: CLI Output Formatting

### JSON Output (Default)

```bash
aws iam list-users --profile admin --output json
```

Output:
```json
{
  "Users": [
    {
      "UserName": "alice-admin",
      "UserId": "AIDAI23HXD2WQ4EXAMPLE",
      "Arn": "arn:aws:iam::123456789012:user/alice-admin",
      "CreateDate": "2026-01-06T10:30:00Z"
    },
    {
      "UserName": "bob-dev",
      ...
    }
  ]
}
```

**When to use:** Scripting, automation, piping to `jq`

### Table Output

```bash
aws iam list-users --profile admin --output table
```

Output:
```
---------------------------------------------------------------
|                         ListUsers                            |
+-------------------------------------------------------------+
||                          Users                             ||
|+------------------+-------------------------+--------------+|
||   UserName       |        UserId           |   Arn        ||
|+------------------+-------------------------+--------------+|
||  alice-admin     |  AIDAI23HXD2WQ4EXAMPLE  |  arn:aws:... ||
||  bob-dev         |  AIDAI44QH8DHBEXAMPLE   |  arn:aws:... ||
|+------------------+-------------------------+--------------+|
```

**When to use:** Human reading, quick visual inspection

### Text Output

```bash
aws iam list-users --profile admin --output text
```

Output:
```
USERS   arn:aws:iam::123456789012:user/alice-admin   2026-01-06T10:30:00Z   alice-admin   AIDAI23HXD2WQ4EXAMPLE
USERS   arn:aws:iam::123456789012:user/bob-dev       2026-01-06T11:45:00Z   bob-dev       AIDAI44QH8DHBEXAMPLE
```

**When to use:** Parsing with `awk`, `cut`, `grep`

### YAML Output

```bash
aws iam list-users --profile admin --output yaml
```

Output:
```yaml
Users:
- UserName: alice-admin
  UserId: AIDAI23HXD2WQ4EXAMPLE
  Arn: arn:aws:iam::123456789012:user/alice-admin
  CreateDate: '2026-01-06T10:30:00Z'
- UserName: bob-dev
  UserId: AIDAI44QH8DHBEXAMPLE
  Arn: arn:aws:iam::123456789012:user/bob-dev
  CreateDate: '2026-01-06T11:45:00Z'
```

**When to use:** Configuration files, more readable than JSON

---

## 💡 Practical Exercise 3.7: Filtering and Querying

### Using --query (JMESPath)

```bash
# Get just user names
aws iam list-users --query "Users[*].UserName" --profile admin --output text

# Output:
alice-admin
bob-dev
charlie-readonly

# Get users created after specific date
aws iam list-users \
  --query "Users[?CreateDate>'2026-01-06'].[UserName,CreateDate]" \
  --profile admin \
  --output table

# Get first 3 users
aws iam list-users --query "Users[:3]" --profile admin

# Get specific fields
aws iam list-users \
  --query "Users[*].{Name:UserName,ID:UserId}" \
  --profile admin \
  --output table
```

**JMESPath cheat sheet:**
- `Users[*]` - All users
- `Users[0]` - First user
- `Users[:3]` - First 3 users
- `Users[?condition]` - Filter
- `{Name:UserName,ID:UserId}` - Custom object

### Using --filter

```bash
# Filter EC2 instances by tag
aws ec2 describe-instances \
  --filters "Name=tag:Environment,Values=production" \
  --profile admin

# Filter S3 objects by prefix
aws s3api list-objects-v2 \
  --bucket my-bucket \
  --prefix "logs/" \
  --profile admin

# Filter IAM users by path
aws iam list-users \
  --path-prefix "/engineering/" \
  --profile admin
```

### Combining with jq (JSON processor)

```bash
# Install jq first
# macOS: brew install jq
# Linux: sudo apt-get install jq

# Get user names only
aws iam list-users --profile admin | jq '.Users[].UserName'

# Count users
aws iam list-users --profile admin | jq '.Users | length'

# Filter users by name pattern
aws iam list-users --profile admin | jq '.Users[] | select(.UserName | contains("admin"))'

# Complex filtering
aws iam list-users --profile admin | jq '
  .Users[] |
  select(.CreateDate > "2026-01-06") |
  {name: .UserName, created: .CreateDate}
'
```

---

## ✅ Verification Steps

### Checklist:

- [ ] AWS CLI v2 installed (`aws --version` shows 2.x.x)
- [ ] Admin profile configured
- [ ] Dev profile configured
- [ ] Prod profile configured
- [ ] Can switch between profiles
- [ ] Tested `aws sts get-caller-identity` for each profile
- [ ] Created profile aliases (optional)
- [ ] Understand `--query` and `--filter`

### Tests:

**Test 1: Profile Switching**
```bash
# Test each profile
export AWS_PROFILE=admin
aws sts get-caller-identity
# Should show admin user ARN

export AWS_PROFILE=dev
aws sts get-caller-identity
# Should show dev user ARN

export AWS_PROFILE=prod
aws sts get-caller-identity
# Should show prod user ARN
```

**Test 2: Region Handling**
```bash
# Check default region for admin profile
aws configure get region --profile admin
# Should show: us-east-1

# Override region for single command
aws ec2 describe-regions --region eu-west-1 --profile admin
```

**Test 3: Output Formats**
```bash
# Try all output formats
aws iam list-users --profile admin --output json
aws iam list-users --profile admin --output table
aws iam list-users --profile admin --output text
aws iam list-users --profile admin --output yaml
```

---

## 🤔 Knowledge Check

### Question 1: Credential Precedence

You have:
1. Environment variables set: `AWS_ACCESS_KEY_ID=AKIAXXX`, `AWS_SECRET_ACCESS_KEY=xxx`
2. Profile configured: `--profile admin`
3. Default profile in `~/.aws/credentials`

You run: `aws s3 ls --profile admin`

Which credentials are used?

<details>
<summary>Click to reveal answer</summary>

**Answer: The `--profile admin` credentials are used.**

**AWS CLI Credential Precedence (highest to lowest):**

1. **Command line options** (`--profile`, `--region`)
2. **Environment variables** (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`)
3. **CLI credentials file** (`~/.aws/credentials`)
4. **CLI config file** (`~/.aws/config`)
5. **Container credentials** (for ECS tasks)
6. **Instance profile credentials** (for EC2 instances)

In this case:
- `--profile admin` is a command line option (precedence #1)
- Environment variables (precedence #2) are ignored because explicit profile specified
- **Result: Uses credentials from `[admin]` section in `~/.aws/credentials`**

**Different scenario:**
```bash
export AWS_ACCESS_KEY_ID=AKIAXXX
export AWS_SECRET_ACCESS_KEY=xxx
aws s3 ls  # No --profile specified
```
**Result: Uses environment variables, not `[default]` profile**

**Best practice:**
Always use `--profile` to be explicit and avoid confusion.

</details>

### Question 2: Region Confusion

You run:
```bash
aws s3 mb s3://my-new-bucket --profile admin
```

Your `admin` profile is configured with `region = eu-west-1`.

**Question:** In which region is the bucket created?

<details>
<summary>Click to reveal answer</summary>

**Answer: It depends on the service!**

**For S3:**
S3 `mb` (make bucket) command uses:
1. `--region` flag if specified
2. If no flag, uses region from profile config (`eu-west-1` in this case)

So bucket is created in **eu-west-1**.

**Verify:**
```bash
aws s3api get-bucket-location --bucket my-new-bucket --profile admin

# Output:
{
  "LocationConstraint": "eu-west-1"
}
```

**Important caveat:**
If you don't specify `--region` and your profile has `region = us-east-1`, the `LocationConstraint` will be `null` (because us-east-1 is the default and doesn't need to be explicitly stated).

**Best practice:**
Always explicitly specify region for clarity:
```bash
aws s3 mb s3://my-new-bucket --region eu-west-1 --profile admin
```

</details>

### Question 3: Why Profiles?

Why use profiles instead of having one set of credentials?

<details>
<summary>Click to reveal answer</summary>

**Answer: Multiple accounts/identities with different permissions**

**Reasons to use multiple profiles:**

**1. Multiple AWS Accounts**
```bash
[profile company-dev]
# Access to development AWS account

[profile company-prod]
# Access to production AWS account (different account ID)
```

**2. Different IAM Users (Same Account)**
```bash
[profile admin]
# Admin user - full access

[profile readonly]
# Readonly user - limited access
```

**3. Different Regions/Settings**
```bash
[profile us-operations]
region = us-east-1
output = json

[profile eu-operations]
region = eu-west-1
output = table
```

**4. Safety**
```bash
# Working in dev, accidentally run destructive command
export AWS_PROFILE=dev
aws s3 rm s3://important-bucket --recursive
# ❌ Error: Access Denied (dev user doesn't have delete permission)
# ✅ Saved! Wrong profile prevented disaster
```

**5. Client Work (Contractors/Consultants)**
```bash
[profile client-a]
# Access to Client A's AWS account

[profile client-b]
# Access to Client B's AWS account
```

**Without profiles:**
- Must manually edit `~/.aws/credentials` every time you switch accounts
- Risk of running commands in wrong account
- Can't easily switch between identities

**With profiles:**
- Switch instantly: `export AWS_PROFILE=dev`
- Clear which account you're using
- Can have different settings per profile

</details>

### Question 4: Finding the Right Command

You want to get details about a specific S3 bucket (creation date, encryption, tags, etc.).

Which command do you use?

<details>
<summary>Click to reveal answer</summary>

**Answer:**

**There are two command sets for S3:**

**1. High-level S3 commands** (aws s3 ...)
- Simple operations: upload, download, copy, delete
- Limited metadata access
- Examples: `aws s3 cp`, `aws s3 ls`, `aws s3 rm`

**2. Low-level S3 API commands** (aws s3api ...)
- Full API access
- Complete metadata and configuration
- Examples: `aws s3api get-bucket-tagging`, `aws s3api put-bucket-encryption`

**For bucket details, use s3api:**

```bash
# Get bucket location
aws s3api get-bucket-location --bucket my-bucket --profile admin

# Get bucket tagging
aws s3api get-bucket-tagging --bucket my-bucket --profile admin

# Get bucket versioning status
aws s3api get-bucket-versioning --bucket my-bucket --profile admin

# Get bucket encryption configuration
aws s3api get-bucket-encryption --bucket my-bucket --profile admin

# Get bucket policy
aws s3api get-bucket-policy --bucket my-bucket --profile admin

# List objects with full metadata
aws s3api list-objects-v2 --bucket my-bucket --profile admin
```

**How to discover commands:**

```bash
# List all S3 commands
aws s3 help
aws s3api help

# Get help for specific command
aws s3api get-bucket-location help

# Search AWS CLI docs
aws s3api <TAB><TAB>  # Press tab twice to see all commands
```

**General pattern:**
- **Simple operations:** Use high-level commands (`aws s3`)
- **Configuration/metadata:** Use API commands (`aws s3api`, `aws iam`, etc.)

</details>

### Question 5: Debugging "Access Denied"

You run:
```bash
aws s3 ls s3://my-bucket/ --profile dev
```

Error:
```
An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied
```

Walk through debugging steps.

<details>
<summary>Click to reveal answer</summary>

**Answer: Systematic debugging approach**

**Step 1: Verify you're using the correct profile**
```bash
aws sts get-caller-identity --profile dev

# Expected: Shows dev user ARN
# If wrong user, check:
echo $AWS_PROFILE  # Should be empty or "dev"
cat ~/.aws/credentials  # Verify [dev] section exists
```

**Step 2: Verify bucket exists**
```bash
aws s3 ls --profile dev

# Is "my-bucket" in the list?
# If not, maybe typo in bucket name
```

**Step 3: Try with admin profile (if you have one)**
```bash
aws s3 ls s3://my-bucket/ --profile admin

# If this works:
#   → Bucket exists, dev user doesn't have permission
# If this fails:
#   → Bucket doesn't exist or different issue
```

**Step 4: Check dev user's permissions**
```bash
# List groups dev user is in
aws iam list-groups-for-user --user-name bob-dev --profile admin

# Check policies attached to those groups
aws iam list-attached-group-policies --group-name developers --profile admin

# Get the policy document
aws iam get-policy --policy-arn arn:aws:iam::123456:policy/DocumentVaultDevPolicy --profile admin
aws iam get-policy-version --policy-arn arn:aws:iam::123456:policy/DocumentVaultDevPolicy --version-id v1 --profile admin
```

**Step 5: Check policy allows ListBucket**
```json
{
  "Effect": "Allow",
  "Action": [
    "s3:ListBucket"  ← Must be present
  ],
  "Resource": "arn:aws:s3:::my-bucket"  ← Must match bucket name
}
```

**Common issues:**

**Issue 1: Missing ListBucket action**
```json
❌ Only has s3:GetObject (can't list)
✅ Needs s3:ListBucket
```

**Issue 2: Wrong resource ARN**
```json
❌ "Resource": "arn:aws:s3:::my-bucket/*"  (objects only)
✅ "Resource": "arn:aws:s3:::my-bucket"   (bucket itself)
```

**Issue 3: Bucket policy blocks access**
```bash
# Check bucket policy
aws s3api get-bucket-policy --bucket my-bucket --profile admin
```

**Issue 4: Wrong region**
```bash
# Buckets are regional, but bucket names are global
# If bucket is in eu-west-1 but you're querying us-east-1:
aws s3 ls s3://my-bucket/ --region eu-west-1 --profile dev
```

**Issue 5: Credentials expired (for temporary credentials)**
```bash
# Check credential expiration
aws sts get-caller-identity --profile dev

# If using temporary credentials (session token), they might have expired
```

**Ultimate debug tool: CloudTrail**
```bash
# CloudTrail logs all API calls
# Go to CloudTrail console, view recent events
# Look for "ListObjectsV2" event
# Check why it was denied (which policy evaluation failed)
```

</details>

---

## ⚠️ Common Mistakes

### Mistake 1: Exposing Credentials

```bash
❌ WRONG:
git add ~/.aws/credentials
git commit -m "Add AWS credentials"
git push

✅ RIGHT:
# Never commit credentials to Git
# Add to .gitignore:
echo ".aws/credentials" >> .gitignore
```

### Mistake 2: Using Root Access Keys

```bash
❌ WRONG:
# Creating access keys for root user
# Using root credentials in CLI

✅ RIGHT:
# Use IAM user access keys
# Root should never have access keys
```

### Mistake 3: Forgetting --profile

```bash
❌ WRONG (if you have multiple accounts):
aws s3 rm s3://bucket --recursive
# Uses [default] profile - might be production!

✅ RIGHT:
aws s3 rm s3://bucket --recursive --profile dev
# Explicit profile - clear which account
```

### Mistake 4: Not Rotating Access Keys

```bash
❌ WRONG:
# Using same access keys for 5 years

✅ RIGHT:
# Rotate every 90 days:
1. Create new access key
2. Update ~/.aws/credentials with new key
3. Test new key works
4. Delete old access key
```

### Mistake 5: Hardcoding Regions

```bash
❌ WRONG:
aws ec2 describe-instances --region us-east-1
# Script breaks if you need to support other regions

✅ RIGHT:
aws ec2 describe-instances --region $AWS_REGION
# Use environment variable or profile config
```

---

## 📊 Day 2-3 Summary

### What You Learned:

- ✅ AWS CLI installation and configuration
- ✅ Creating and managing multiple profiles
- ✅ Understanding credential precedence
- ✅ Essential CLI commands for S3, IAM, EC2
- ✅ Output formatting and filtering
- ✅ Debugging CLI issues

### Resources Created:

| Resource | Purpose | Cost |
|----------|---------|------|
| AWS CLI v2 installed | Command-line access | Free |
| 3 CLI profiles | admin, dev, prod | Free |
| Access keys | CLI authentication | Free |

**Total Cost: $0.00**

### Next: Regions and S3

Tomorrow you'll:
- Understand AWS global infrastructure
- Learn about regions and availability zones
- Create multi-region S3 buckets
- Configure bucket security

---

Would you like me to continue with the remaining days (Regions & S3, IAM Policies Deep Dive, Billing & Cost Management, Security, Troubleshooting, and Final Capstone)? The tutorial is comprehensive and structured to teach through both theory and hands-on practice.