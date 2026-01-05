# 🚀 Phase 0 Learning Project: Multi-Region Document Vault

**Target Time:** 5-7 days
**Difficulty:** Beginner
**Goal:** Build a secure, multi-region document storage system using only AWS fundamentals (IAM, S3, CLI, tagging)

---

## 📋 Project Overview

Build a **Document Vault** that allows different types of users (admins, developers, readonly) to securely store and access documents across multiple AWS regions. This project will force you to deeply understand IAM, CLI operations, regions, security, and cost management.

### What You'll Build

- Multi-region S3 bucket architecture
- IAM users, groups, and roles with least-privilege access
- CLI-based upload/download workflows
- Proper tagging and cost tracking
- Secure authentication and authorization flows
- Billing alerts and cost monitoring

---

## 🎯 Learning Objectives Covered

This project maps directly to Phase 0 concepts:

- ✅ AWS global infrastructure (regions, AZs)
- ✅ IAM (users, groups, roles, policies)
- ✅ AWS CLI proficiency
- ✅ Billing safety and cost management
- ✅ Resource tagging best practices
- ✅ Authentication vs authorization
- ✅ Secrets management
- ✅ Core AWS services (S3, IAM, CloudWatch)
- ✅ Break and recover mindset

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────┐
│                 AWS Account (Root)                   │
│  ✓ MFA enabled                                       │
│  ✓ Root locked away                                  │
│  ✓ Billing alerts configured                         │
└─────────────────────────────────────────────────────┘
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    ┌───▼───┐        ┌────▼────┐      ┌────▼────┐
    │ IAM   │        │  IAM    │      │  IAM    │
    │Admins │        │  Devs   │      │ Readonly│
    └───┬───┘        └────┬────┘      └────┬────┘
        │                 │                 │
        └─────────────────┼─────────────────┘
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    ┌───▼──────┐     ┌────▼─────┐     ┌────▼─────┐
    │ Region 1  │     │ Region 2 │     │ Region 3 │
    │ (Primary) │     │ (Backup) │     │  (DR)    │
    │           │     │          │     │          │
    │ S3: docs- │     │ S3: docs-│     │ S3: docs-│
    │ us-east-1 │     │ eu-west-1│     │ ap-south-│
    │           │     │          │     │  east-2  │
    └───────────┘     └──────────┘     └──────────┘
```

---

## 📝 Project Requirements

### Part 1: Account Setup & Security (Day 1)

**Tasks:**

1. **Secure the Root Account**
   - Enable MFA on root
   - Lock root credentials in password manager
   - Document root account email

2. **Create IAM Admin User**
   - Username: `admin-yourname`
   - Enable programmatic and console access
   - Attach `AdministratorAccess` policy
   - Enable MFA for admin user

3. **Billing Safety Setup**
   - Enable Cost Explorer
   - Create billing alert for $5
   - Create billing alert for $10
   - Set up free tier usage alerts
   - Enable cost allocation tags

4. **Verification**
   - Take screenshots of billing alerts
   - Document admin user ARN
   - Test login to console as admin

**Success Criteria:**
- Root account locked and secured
- Admin user functional
- Billing alerts confirmed via email

---

### Part 2: IAM Structure (Day 2)

**Tasks:**

1. **Create IAM Groups**
   ```bash
   # Create groups
   aws iam create-group --group-name admins
   aws iam create-group --group-name developers
   aws iam create-group --group-name readonly
   ```

2. **Create IAM Policies**
   - `DocumentVaultAdminPolicy` - Full access to all buckets
   - `DocumentVaultDevPolicy` - Read/write to dev buckets only
   - `DocumentVaultReadPolicy` - Read-only access

3. **Create IAM Users**
   - `alice-admin` (member of admins)
   - `bob-dev` (member of developers)
   - `charlie-readonly` (member of readonly)

4. **Create IAM Role**
   - Role name: `EC2-DocumentVault-Role`
   - Trust policy: EC2 service
   - Permission: S3 read-only access
   - Purpose: Future EC2 instances can read documents

**Success Criteria:**
- All groups created with correct policies
- All users can log in
- Role ready for future use

**Challenge Questions:**
- Why use groups instead of attaching policies directly to users?
- Why is a role safer than access keys for EC2?
- What happens if you delete a user that owns resources?

---

### Part 3: CLI Configuration (Day 2-3)

**Tasks:**

1. **Install AWS CLI v2**
   ```bash
   # Verify installation
   aws --version
   ```

2. **Configure Multiple Profiles**
   ```bash
   # Admin profile
   aws configure --profile admin
   # Access Key ID: [admin user key]
   # Secret: [admin user secret]
   # Region: us-east-1
   # Output: json

   # Dev profile
   aws configure --profile dev
   # Region: us-east-1

   # Prod profile (for future)
   aws configure --profile prod
   # Region: eu-west-1
   ```

3. **Verify Each Profile**
   ```bash
   aws sts get-caller-identity --profile admin
   aws sts get-caller-identity --profile dev
   ```

4. **Environment Variable Practice**
   ```bash
   export AWS_PROFILE=admin
   aws sts get-caller-identity

   export AWS_DEFAULT_REGION=ap-southeast-2
   aws ec2 describe-regions
   ```

**Success Criteria:**
- Can switch between profiles seamlessly
- Understand ~/.aws/credentials vs ~/.aws/config
- Never hardcoded keys in scripts

---

### Part 4: Multi-Region S3 Architecture (Day 3-4)

**Tasks:**

1. **Create Buckets in 3 Regions**

   **Primary Region (us-east-1):**
   ```bash
   aws s3 mb s3://docvault-primary-yourname-useast1 \
     --region us-east-1 \
     --profile admin
   ```

   **Backup Region (eu-west-1):**
   ```bash
   aws s3 mb s3://docvault-backup-yourname-euwest1 \
     --region eu-west-1 \
     --profile admin
   ```

   **DR Region (ap-southeast-2):**
   ```bash
   aws s3 mb s3://docvault-dr-yourname-apsouth2 \
     --region ap-southeast-2 \
     --profile admin
   ```

2. **Apply Bucket Tags**
   ```bash
   aws s3api put-bucket-tagging \
     --bucket docvault-primary-yourname-useast1 \
     --tagging 'TagSet=[
       {Key=Name,Value=DocVault-Primary},
       {Key=Environment,Value=Production},
       {Key=Owner,Value=YourName},
       {Key=Project,Value=DocumentVault},
       {Key=CostCenter,Value=Learning},
       {Key=Region,Value=Primary}
     ]'
   ```

   Repeat for other buckets with appropriate tags.

3. **Enable Bucket Versioning**
   ```bash
   aws s3api put-bucket-versioning \
     --bucket docvault-primary-yourname-useast1 \
     --versioning-configuration Status=Enabled
   ```

4. **Block Public Access**
   ```bash
   aws s3api put-public-access-block \
     --bucket docvault-primary-yourname-useast1 \
     --public-access-block-configuration \
       "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"
   ```

**Success Criteria:**
- 3 buckets in 3 different regions
- All properly tagged
- Public access blocked
- Versioning enabled

**Challenge:**
- Try listing buckets from eu-west-1 region - what do you see?
- Why are S3 bucket names global but buckets themselves regional?

---

### Part 5: Access Control Testing (Day 4-5)

**Tasks:**

1. **Test Admin Access**
   ```bash
   # As admin, upload to all buckets
   echo "Admin test file" > admin-test.txt

   aws s3 cp admin-test.txt s3://docvault-primary-yourname-useast1/ \
     --profile admin

   aws s3 cp admin-test.txt s3://docvault-backup-yourname-euwest1/ \
     --profile admin
   ```

2. **Test Developer Access**
   ```bash
   # As dev, try to upload
   aws s3 cp admin-test.txt s3://docvault-primary-yourname-useast1/ \
     --profile dev

   # Expected: AccessDenied
   # Fix: Update DocumentVaultDevPolicy
   ```

3. **Test Readonly Access**
   ```bash
   # As readonly, try to read
   aws s3 ls s3://docvault-primary-yourname-useast1/ \
     --profile readonly

   # Try to write (should fail)
   echo "test" > test.txt
   aws s3 cp test.txt s3://docvault-primary-yourname-useast1/ \
     --profile readonly

   # Expected: AccessDenied
   ```

4. **IAM Policy Simulator**
   - Go to IAM console
   - Use Policy Simulator
   - Test each user's permissions
   - Document which actions are allowed/denied

**Success Criteria:**
- Admin has full access
- Dev has read/write (after policy fix)
- Readonly has read-only
- Can explain why each fails or succeeds

---

### Part 6: Document Upload Workflow (Day 5)

**Tasks:**

1. **Create Sample Documents**
   ```bash
   mkdir -p sample-docs/{public,confidential,internal}

   echo "Public documentation" > sample-docs/public/readme.txt
   echo "Confidential data" > sample-docs/confidential/secrets.txt
   echo "Internal notes" > sample-docs/internal/notes.txt
   ```

2. **Upload with Metadata**
   ```bash
   aws s3 cp sample-docs/public/readme.txt \
     s3://docvault-primary-yourname-useast1/public/ \
     --metadata "Classification=Public,Owner=Alice,UploadDate=$(date +%Y-%m-%d)" \
     --profile admin
   ```

3. **Cross-Region Copy**
   ```bash
   # Copy from primary to backup
   aws s3 sync s3://docvault-primary-yourname-useast1/ \
     s3://docvault-backup-yourname-euwest1/ \
     --source-region us-east-1 \
     --region eu-west-1 \
     --profile admin
   ```

4. **Download and Verify**
   ```bash
   aws s3 cp s3://docvault-backup-yourname-euwest1/public/readme.txt \
     ./downloaded-readme.txt \
     --region eu-west-1 \
     --profile admin

   diff sample-docs/public/readme.txt downloaded-readme.txt
   ```

**Success Criteria:**
- Files uploaded with proper metadata
- Cross-region replication works
- Downloads are bit-for-bit identical

---

### Part 7: Cost Monitoring (Day 6)

**Tasks:**

1. **Check Current Costs**
   ```bash
   # View billing information
   aws ce get-cost-and-usage \
     --time-period Start=2026-01-01,End=2026-01-06 \
     --granularity DAILY \
     --metrics "UnblendedCost" \
     --profile admin
   ```

2. **Tag-Based Cost Allocation**
   - Go to Cost Explorer
   - Filter by tag: Project=DocumentVault
   - View cost breakdown by region
   - Export cost report

3. **Resource Inventory**
   ```bash
   # List all buckets
   aws s3 ls --profile admin

   # Count objects per bucket
   aws s3 ls s3://docvault-primary-yourname-useast1/ --recursive \
     --summarize --profile admin
   ```

4. **Calculate Costs**
   - S3 storage: $0.023 per GB
   - S3 PUT requests: $0.005 per 1,000
   - Data transfer: $0.09 per GB
   - Estimate your monthly cost

**Success Criteria:**
- Understand exactly what you're paying for
- Can predict costs for next month
- Billing alerts are working

---

### Part 8: Security Hardening (Day 6-7)

**Tasks:**

1. **Rotate Access Keys**
   ```bash
   # Create new access key for admin user
   aws iam create-access-key --user-name admin-yourname \
     --profile admin

   # Update ~/.aws/credentials
   # Test new key
   aws sts get-caller-identity --profile admin

   # Delete old key
   aws iam delete-access-key \
     --access-key-id AKIAXXXXXXXX \
     --user-name admin-yourname \
     --profile admin
   ```

2. **Enable CloudTrail Logging**
   ```bash
   aws cloudtrail create-trail \
     --name docvault-audit-trail \
     --s3-bucket-name docvault-primary-yourname-useast1 \
     --profile admin

   aws cloudtrail start-logging \
     --name docvault-audit-trail \
     --profile admin
   ```

3. **Bucket Policy for Encryption**
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "DenyUnencryptedObjectUploads",
         "Effect": "Deny",
         "Principal": "*",
         "Action": "s3:PutObject",
         "Resource": "arn:aws:s3:::docvault-primary-yourname-useast1/*",
         "Condition": {
           "StringNotEquals": {
             "s3:x-amz-server-side-encryption": "AES256"
           }
         }
       }
     ]
   }
   ```

4. **Environment Variable Security**
   ```bash
   # WRONG - never do this
   export AWS_ACCESS_KEY_ID=AKIAXXXXXXXX
   export AWS_SECRET_ACCESS_KEY=xxxxx

   # RIGHT - use profiles
   export AWS_PROFILE=admin
   ```

**Success Criteria:**
- Access keys rotated successfully
- CloudTrail logging all actions
- Encryption enforced on uploads
- No secrets in Git or scripts

---

### Part 9: Break & Recover Exercise (Day 7)

**Tasks:**

1. **Deliberately Break IAM**
   ```bash
   # Remove permissions from dev user
   aws iam detach-user-policy \
     --user-name bob-dev \
     --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess \
     --profile admin

   # Try to access as dev
   aws s3 ls --profile dev
   # Expected: AccessDenied

   # Fix it
   aws iam attach-user-policy \
     --user-name bob-dev \
     --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess \
     --profile admin
   ```

2. **Delete and Recreate CLI Config**
   ```bash
   # Backup
   cp ~/.aws/credentials ~/.aws/credentials.backup

   # Delete
   rm ~/.aws/credentials

   # Try commands (will fail)
   aws s3 ls --profile admin

   # Restore
   mv ~/.aws/credentials.backup ~/.aws/credentials
   ```

3. **Recover from Accidental Delete**
   ```bash
   # Accidentally delete a file
   aws s3 rm s3://docvault-primary-yourname-useast1/public/readme.txt \
     --profile admin

   # Recover using versioning
   aws s3api list-object-versions \
     --bucket docvault-primary-yourname-useast1 \
     --prefix public/readme.txt \
     --profile admin

   # Restore previous version
   aws s3api copy-object \
     --copy-source "docvault-primary-yourname-useast1/public/readme.txt?versionId=VERSION_ID" \
     --bucket docvault-primary-yourname-useast1 \
     --key public/readme.txt \
     --profile admin
   ```

4. **Simulate Region Failure**
   ```bash
   # Pretend us-east-1 is down
   # Can you access backup in eu-west-1?

   aws s3 ls s3://docvault-backup-yourname-euwest1/ \
     --region eu-west-1 \
     --profile admin
   ```

**Success Criteria:**
- Successfully recovered from each break
- Understand why versioning matters
- Comfortable with troubleshooting

---

### Part 10: Final Capstone Challenge (Day 7)

**Complete this workflow from memory:**

1. Create new IAM user `test-user`
2. Add to developers group
3. Configure CLI profile for test-user
4. Verify identity
5. Create S3 bucket in ap-south-1
6. Upload 3 files with tags
7. List bucket contents
8. Download one file
9. Check costs for today
10. Delete all resources
11. Verify deletion

**Time limit:** 30 minutes

**Success Criteria:**
- Complete without looking at notes
- No errors during execution
- All resources cleaned up
- Can explain each step

---

## 🎓 Knowledge Check Questions

Before moving to Phase 1, you must confidently answer:

### IAM & Security
1. What's the difference between a user and a role?
2. Why should you never use root credentials daily?
3. What happens if you attach conflicting policies to a user?
4. How does IAM Policy Simulator help?
5. When should you use identity-based vs resource-based policies?

### Regions & Global Infrastructure
1. Why can't EC2 in us-east-1 directly access an instance in eu-west-1?
2. What happens to data if an entire AZ fails?
3. Why are S3 bucket names globally unique but buckets regional?
4. What's the difference between a region and an edge location?
5. How does cross-region replication work?

### CLI & Operations
1. Where are CLI credentials stored?
2. How do you switch between profiles?
3. What does `aws sts get-caller-identity` tell you?
4. How do you specify a region for a single command?
5. Why is `--profile` safer than environment variables?

### Cost & Billing
1. What generates costs in S3?
2. How do tags help with cost tracking?
3. Why set multiple billing alerts?
4. What's the difference between Cost Explorer and billing alerts?
5. How can you prevent unexpected charges?

### Practical Scenarios
1. User reports "AccessDenied" - where do you look first?
2. File accidentally deleted - how to recover?
3. Need to grant temporary access - user or role?
4. Data must stay in Europe - how to enforce?
5. CLI commands failing - troubleshooting steps?

---

## 🎯 Success Metrics

You've completed Phase 0 when you can:

- ✅ Set up AWS account safely (billing, MFA, IAM)
- ✅ Create users, groups, roles with least privilege
- ✅ Use CLI fluently with multiple profiles
- ✅ Manage resources across multiple regions
- ✅ Apply proper tagging for cost tracking
- ✅ Troubleshoot IAM permission issues
- ✅ Recover from accidental deletions
- ✅ Explain AWS fundamentals to a beginner
- ✅ Feel confident, not anxious about costs
- ✅ Complete the capstone in under 30 minutes

---

## 📊 Project Deliverables

Document your work:

1. **Architecture Diagram** (draw.io or hand-drawn)
2. **IAM Policy Documents** (save as JSON)
3. **CLI Command History** (essential commands)
4. **Cost Report** (final billing screenshot)
5. **Lessons Learned** (what was hard, what clicked)
6. **Knowledge Check Answers** (written explanations)

---

## 🚀 Extensions (Optional)

Once you master the basics:

1. **Automate with Shell Scripts**
   - Create upload.sh script
   - Add error handling
   - Implement retry logic

2. **Add Lambda Function**
   - Trigger on S3 upload
   - Log to CloudWatch
   - Use IAM role (not keys)

3. **Implement Lifecycle Policies**
   - Move old files to Glacier
   - Delete after 90 days
   - Tag-based rules

4. **Add AWS Organizations**
   - Create dev/prod accounts
   - Use SCPs
   - Centralized billing

5. **Infrastructure as Code**
   - Convert to Terraform
   - Use variables for regions
   - Output bucket ARNs

---

## 🛠️ Tools You'll Use

- AWS Console
- AWS CLI v2
- Text editor (for policies)
- Terminal
- IAM Policy Simulator
- Cost Explorer
- CloudTrail
- (Optional) draw.io for diagrams

---

## 💡 Pro Tips

1. **Tag Everything** - Future you will thank current you
2. **Use Aliases** - `alias awsadmin='aws --profile admin'`
3. **Document Decisions** - Why did you choose this policy?
4. **Test Permissions** - Use `--dry-run` when possible
5. **Clean Up Daily** - Avoid surprise charges
6. **Break Intentionally** - Recovery builds confidence
7. **Explain Out Loud** - Teaching solidifies learning
8. **Compare Regions** - Notice latency differences
9. **Read Error Messages** - They're incredibly specific
10. **Bookmark Docs** - IAM policy reference, CLI docs

---

## 🎬 Next Steps After Completion

Once you finish this project:

1. **Review Phase 0 Checklist** - tick everything off
2. **Take Knowledge Check** - score 90%+
3. **Complete Capstone** - under 30 minutes
4. **Document Lessons** - what stuck, what didn't
5. **Share Your Work** - blog, GitHub, LinkedIn
6. **Move to Phase 1** - Networking (VPC, subnets, NAT)

---

## 📚 Additional Resources

- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS CLI Command Reference](https://awscli.amazonaws.com/v2/documentation/api/latest/index.html)
- [S3 Pricing Calculator](https://calculator.aws/)
- [AWS Free Tier Details](https://aws.amazon.com/free/)
- [Tagging Best Practices](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html)

---

## ⚠️ Common Pitfalls

1. **Using root account daily** - Lock it away!
2. **Hardcoding credentials** - Use profiles always
3. **Forgetting regions** - Resources are regional
4. **No billing alerts** - Recipe for disaster
5. **Skip tagging** - Chaos in 3 months
6. **Too many permissions** - Least privilege always
7. **No versioning** - Can't recover from mistakes
8. **Public S3 buckets** - Security nightmare
9. **No MFA** - Account compromise risk
10. **Skipping cleanup** - Paying for unused resources

---

**Remember:** Phase 0 is the foundation. Everything else builds on this. If IAM, CLI, and regions feel shaky, Phase 1 (networking) will be painful. Take your time here. Master the basics.

**When you finish this project, Phase 1 should feel easy, not overwhelming.**

Good luck! 🚀
