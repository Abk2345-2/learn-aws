# 🌐 Phase 1 Learning Project: Multi-Tier Web Application Infrastructure

**Target Time:** 10-14 days
**Difficulty:** Intermediate
**Prerequisites:** Phase 0 completed (IAM, CLI, S3, regions)
**Goal:** Build a production-grade VPC with public/private subnets, routing, NAT, and security layers that host a real multi-tier application

---

## 📋 Project Overview

Build a **complete AWS networking foundation** for a three-tier web application (web tier, app tier, database tier) across multiple availability zones. You'll learn VPC design, subnet architecture, routing, NAT gateways, security groups, NACLs, and traffic flow debugging.

### What You'll Build

- Custom VPC with proper CIDR planning
- Multi-AZ architecture (high availability)
- Public subnets for load balancers and NAT
- Private subnets for application servers
- Isolated subnets for databases
- Internet Gateway for public traffic
- NAT Gateway for private subnet internet access
- Security Groups with least-privilege rules
- Network ACLs for subnet-level protection
- Bastion host for secure SSH access

---

## 🎯 Learning Objectives Covered

This project maps directly to Phase 1 concepts:

- ✅ VPC fundamentals and CIDR notation
- ✅ Subnets and Availability Zone mapping
- ✅ Internet Gateway (IGW) configuration
- ✅ NAT Gateway and private subnet internet access
- ✅ Route tables and routing logic
- ✅ Security Groups (stateful) vs NACLs (stateless)
- ✅ Traffic flow analysis and debugging
- ✅ Cost awareness (NAT, EIP, data transfer)
- ✅ CLI proficiency for networking
- ✅ Break and recover methodology

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Region: us-east-1                        │
│                    VPC: 10.0.0.0/16 (65,536 IPs)                │
└─────────────────────────────────────────────────────────────────┘
                                 │
        ┌────────────────────────┼────────────────────────┐
        │                        │                        │
┌───────▼────────┐      ┌────────▼────────┐      ┌──────▼────────┐
│  AZ us-east-1a │      │  AZ us-east-1b  │      │ AZ us-east-1c │
│                │      │                 │      │               │
│ ┌────────────┐ │      │ ┌─────────────┐│      │┌────────────┐ │
│ │Public      │ │      │ │Public       ││      ││Public      │ │
│ │10.0.1.0/24 │ │      │ │10.0.2.0/24  ││      ││10.0.3.0/24 │ │
│ │            │ │      │ │             ││      ││            │ │
│ │- NAT-GW    │ │      │ │- NAT-GW     ││      ││- NAT-GW    │ │
│ │- Bastion   │ │      │ │- ALB        ││      ││- ALB       │ │
│ └────────────┘ │      │ └─────────────┘│      │└────────────┘ │
│                │      │                 │      │               │
│ ┌────────────┐ │      │ ┌─────────────┐│      │┌────────────┐ │
│ │Private     │ │      │ │Private      ││      ││Private     │ │
│ │10.0.11.0/24│ │      │ │10.0.12.0/24 ││      ││10.0.13.0/24│ │
│ │            │ │      │ │             ││      ││            │ │
│ │- App       │ │      │ │- App        ││      ││- App       │ │
│ │  Server    │ │      │ │  Server     ││      ││  Server    │ │
│ └────────────┘ │      │ └─────────────┘│      │└────────────┘ │
│                │      │                 │      │               │
│ ┌────────────┐ │      │ ┌─────────────┐│      │┌────────────┐ │
│ │Isolated    │ │      │ │Isolated     ││      ││Isolated    │ │
│ │10.0.21.0/24│ │      │ │10.0.22.0/24 ││      ││10.0.23.0/24│ │
│ │            │ │      │ │             ││      ││            │ │
│ │- RDS       │ │      │ │- RDS        ││      ││- RDS       │ │
│ │  (Primary) │ │      │ │  (Standby)  ││      ││  (Replica) │ │
│ └────────────┘ │      │ └─────────────┘│      │└────────────┘ │
└────────────────┘      └─────────────────┘      └───────────────┘
        │                        │                        │
        └────────────────────────┼────────────────────────┘
                                 │
                         ┌───────▼────────┐
                         │ Internet       │
                         │ Gateway (IGW)  │
                         └────────────────┘
                                 │
                         ┌───────▼────────┐
                         │   Internet     │
                         └────────────────┘

Traffic Flows:
→ Internet → IGW → ALB (public) → App Server (private) → RDS (isolated)
→ App Server (private) → NAT-GW → IGW → Internet (for updates)
→ Developer → Bastion (public) → App Server (private)
```

---

## 📝 Project Requirements

### Part 1: CIDR Planning & VPC Design (Day 1-2)

**Tasks:**

1. **Learn CIDR Notation**
   ```bash
   # /16 = 65,536 IPs (VPC level)
   # /24 = 256 IPs (subnet level)
   # AWS reserves 5 IPs per subnet:
   #   - .0 (network address)
   #   - .1 (VPC router)
   #   - .2 (DNS server)
   #   - .3 (future use)
   #   - .255 (broadcast)

   # So 10.0.1.0/24 has 256 - 5 = 251 usable IPs
   ```

2. **Design CIDR Allocation**

   Create a spreadsheet or document:

   | Subnet Type | AZ | CIDR | Usable IPs | Purpose |
   |------------|-----|------|------------|---------|
   | Public | us-east-1a | 10.0.1.0/24 | 251 | NAT, Bastion |
   | Public | us-east-1b | 10.0.2.0/24 | 251 | ALB |
   | Public | us-east-1c | 10.0.3.0/24 | 251 | ALB |
   | Private | us-east-1a | 10.0.11.0/24 | 251 | App servers |
   | Private | us-east-1b | 10.0.12.0/24 | 251 | App servers |
   | Private | us-east-1c | 10.0.13.0/24 | 251 | App servers |
   | Isolated | us-east-1a | 10.0.21.0/24 | 251 | RDS Primary |
   | Isolated | us-east-1b | 10.0.22.0/24 | 251 | RDS Standby |
   | Isolated | us-east-1c | 10.0.23.0/24 | 251 | RDS Replica |

3. **Calculate IP Requirements**
   - Total VPC: 10.0.0.0/16 = 65,536 IPs
   - Used: 9 × 256 = 2,304 IPs
   - Available for future: 63,232 IPs
   - Growth capacity: Room for 247 more /24 subnets

4. **Document Architecture Decisions**
   - Why 3 AZs? (High availability + DR)
   - Why /24 subnets? (Standard practice, easy to manage)
   - Why separate isolated tier? (Database security)
   - Why this CIDR scheme? (Easy to remember, logical grouping)

**Success Criteria:**
- Can explain CIDR notation confidently
- Understand IP capacity calculation
- Have clear subnet design document

**Challenge Questions:**
- Why use 10.0.0.0/16 instead of 10.0.0.0/8?
- What happens if you need more than 251 IPs in a subnet?
- Can you change VPC CIDR after creation?

---

### Part 2: VPC Creation (Day 2-3)

**Tasks:**

1. **Create Custom VPC**
   ```bash
   # Create VPC
   aws ec2 create-vpc \
     --cidr-block 10.0.0.0/16 \
     --tag-specifications 'ResourceType=vpc,Tags=[
       {Key=Name,Value=webapp-vpc},
       {Key=Environment,Value=production},
       {Key=Project,Value=WebApp},
       {Key=ManagedBy,Value=YourName}
     ]' \
     --profile admin

   # Save VPC ID
   export VPC_ID=vpc-xxxxxxxxx

   # Enable DNS hostnames
   aws ec2 modify-vpc-attribute \
     --vpc-id $VPC_ID \
     --enable-dns-hostnames \
     --profile admin

   # Enable DNS support
   aws ec2 modify-vpc-attribute \
     --vpc-id $VPC_ID \
     --enable-dns-support \
     --profile admin
   ```

2. **Create Subnets (All 9)**

   **Public Subnets:**
   ```bash
   # us-east-1a
   aws ec2 create-subnet \
     --vpc-id $VPC_ID \
     --cidr-block 10.0.1.0/24 \
     --availability-zone us-east-1a \
     --tag-specifications 'ResourceType=subnet,Tags=[
       {Key=Name,Value=webapp-public-1a},
       {Key=Type,Value=public},
       {Key=AZ,Value=us-east-1a}
     ]' \
     --profile admin

   # us-east-1b
   aws ec2 create-subnet \
     --vpc-id $VPC_ID \
     --cidr-block 10.0.2.0/24 \
     --availability-zone us-east-1b \
     --tag-specifications 'ResourceType=subnet,Tags=[
       {Key=Name,Value=webapp-public-1b},
       {Key=Type,Value=public},
       {Key=AZ,Value=us-east-1b}
     ]' \
     --profile admin

   # us-east-1c (repeat pattern)
   ```

   **Private Subnets:**
   ```bash
   # Repeat for 10.0.11.0/24, 10.0.12.0/24, 10.0.13.0/24
   # Tag with Type=private
   ```

   **Isolated Subnets:**
   ```bash
   # Repeat for 10.0.21.0/24, 10.0.22.0/24, 10.0.23.0/24
   # Tag with Type=isolated
   ```

3. **Verify Subnet Creation**
   ```bash
   # List all subnets in VPC
   aws ec2 describe-subnets \
     --filters "Name=vpc-id,Values=$VPC_ID" \
     --query 'Subnets[*].[SubnetId,CidrBlock,AvailabilityZone,Tags[?Key==`Name`].Value|[0]]' \
     --output table \
     --profile admin
   ```

4. **Enable Auto-Assign Public IP for Public Subnets**
   ```bash
   # For each public subnet
   aws ec2 modify-subnet-attribute \
     --subnet-id subnet-xxxxxxxxx \
     --map-public-ip-on-launch \
     --profile admin
   ```

**Success Criteria:**
- VPC created with correct CIDR
- All 9 subnets created in correct AZs
- DNS enabled on VPC
- Public subnets auto-assign public IPs

---

### Part 3: Internet Gateway & Public Routing (Day 3-4)

**Tasks:**

1. **Create Internet Gateway**
   ```bash
   # Create IGW
   aws ec2 create-internet-gateway \
     --tag-specifications 'ResourceType=internet-gateway,Tags=[
       {Key=Name,Value=webapp-igw},
       {Key=VPC,Value=webapp-vpc}
     ]' \
     --profile admin

   # Save IGW ID
   export IGW_ID=igw-xxxxxxxxx

   # Attach to VPC
   aws ec2 attach-internet-gateway \
     --vpc-id $VPC_ID \
     --internet-gateway-id $IGW_ID \
     --profile admin
   ```

2. **Create Public Route Table**
   ```bash
   # Create route table
   aws ec2 create-route-table \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=route-table,Tags=[
       {Key=Name,Value=webapp-public-rt},
       {Key=Type,Value=public}
     ]' \
     --profile admin

   # Save route table ID
   export PUBLIC_RT_ID=rtb-xxxxxxxxx

   # Add route to IGW (0.0.0.0/0 → IGW)
   aws ec2 create-route \
     --route-table-id $PUBLIC_RT_ID \
     --destination-cidr-block 0.0.0.0/0 \
     --gateway-id $IGW_ID \
     --profile admin
   ```

3. **Associate Public Subnets to Public Route Table**
   ```bash
   # For each public subnet
   aws ec2 associate-route-table \
     --subnet-id subnet-xxxxxxxxx \
     --route-table-id $PUBLIC_RT_ID \
     --profile admin

   # Repeat for all 3 public subnets
   ```

4. **Verify Public Routing**
   ```bash
   # Describe route table
   aws ec2 describe-route-tables \
     --route-table-ids $PUBLIC_RT_ID \
     --profile admin

   # Should see:
   # - 10.0.0.0/16 → local
   # - 0.0.0.0/0 → igw-xxxxx
   ```

**Success Criteria:**
- IGW attached to VPC
- Public route table has 0.0.0.0/0 → IGW
- All public subnets associated with public route table
- Can explain why public subnets need IGW route

**Test:**
- Launch EC2 in public subnet
- Should get public IP automatically
- Should be able to ping 8.8.8.8

---

### Part 4: NAT Gateway & Private Routing (Day 4-6)

**Tasks:**

1. **Allocate Elastic IPs (one per NAT Gateway)**
   ```bash
   # AZ-1a NAT
   aws ec2 allocate-address \
     --domain vpc \
     --tag-specifications 'ResourceType=elastic-ip,Tags=[
       {Key=Name,Value=webapp-nat-eip-1a},
       {Key=AZ,Value=us-east-1a}
     ]' \
     --profile admin

   export NAT_EIP_1A=eipalloc-xxxxxxxxx

   # Repeat for us-east-1b and us-east-1c
   ```

2. **Create NAT Gateways (one per AZ for HA)**
   ```bash
   # NAT in us-east-1a (in public subnet)
   aws ec2 create-nat-gateway \
     --subnet-id $PUBLIC_SUBNET_1A \
     --allocation-id $NAT_EIP_1A \
     --tag-specifications 'ResourceType=natgateway,Tags=[
       {Key=Name,Value=webapp-nat-1a},
       {Key=AZ,Value=us-east-1a}
     ]' \
     --profile admin

   export NAT_GW_1A=nat-xxxxxxxxx

   # Wait for NAT Gateway to become available
   aws ec2 describe-nat-gateways \
     --nat-gateway-ids $NAT_GW_1A \
     --profile admin

   # Repeat for us-east-1b and us-east-1c
   ```

3. **Create Private Route Tables (one per AZ)**
   ```bash
   # Private route table for AZ-1a
   aws ec2 create-route-table \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=route-table,Tags=[
       {Key=Name,Value=webapp-private-rt-1a},
       {Key=Type,Value=private},
       {Key=AZ,Value=us-east-1a}
     ]' \
     --profile admin

   export PRIVATE_RT_1A=rtb-xxxxxxxxx

   # Add route to NAT Gateway
   aws ec2 create-route \
     --route-table-id $PRIVATE_RT_1A \
     --destination-cidr-block 0.0.0.0/0 \
     --nat-gateway-id $NAT_GW_1A \
     --profile admin

   # Associate private subnet 1a to this route table
   aws ec2 associate-route-table \
     --subnet-id $PRIVATE_SUBNET_1A \
     --route-table-id $PRIVATE_RT_1A \
     --profile admin

   # Repeat for AZ-1b and AZ-1c
   ```

4. **Create Isolated Route Table (No Internet)**
   ```bash
   # Isolated route table (for databases)
   aws ec2 create-route-table \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=route-table,Tags=[
       {Key=Name,Value=webapp-isolated-rt},
       {Key=Type,Value=isolated}
     ]' \
     --profile admin

   export ISOLATED_RT=rtb-xxxxxxxxx

   # NO default route added (no internet access)
   # Only has local route (10.0.0.0/16)

   # Associate all isolated subnets
   aws ec2 associate-route-table \
     --subnet-id $ISOLATED_SUBNET_1A \
     --route-table-id $ISOLATED_RT \
     --profile admin

   # Repeat for all isolated subnets
   ```

5. **Cost Awareness Check**
   ```bash
   # NAT Gateway costs:
   # - $0.045 per hour (even if idle)
   # - $0.045 per GB processed
   # - 3 NAT Gateways × 24h × 30 days = ~$97/month

   # Document in cost tracking sheet
   # Set up billing alert for NAT costs
   ```

**Success Criteria:**
- 3 NAT Gateways running (one per AZ)
- 3 private route tables (each pointing to its AZ's NAT)
- 1 isolated route table (no internet route)
- Understand why NAT must be in public subnet
- Can explain NAT costs

**Test:**
- Launch EC2 in private subnet
- No public IP assigned
- Can SSH via bastion (next section)
- Can `curl google.com` (works via NAT)
- From internet, cannot reach private EC2

---

### Part 5: Security Groups & Traffic Control (Day 6-7)

**Tasks:**

1. **Create Bastion Host Security Group**
   ```bash
   aws ec2 create-security-group \
     --group-name webapp-bastion-sg \
     --description "Bastion host SSH access" \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=security-group,Tags=[
       {Key=Name,Value=webapp-bastion-sg}
     ]' \
     --profile admin

   export BASTION_SG=sg-xxxxxxxxx

   # Allow SSH from your IP only
   MY_IP=$(curl -s ifconfig.me)

   aws ec2 authorize-security-group-ingress \
     --group-id $BASTION_SG \
     --protocol tcp \
     --port 22 \
     --cidr $MY_IP/32 \
     --profile admin
   ```

2. **Create Application Server Security Group**
   ```bash
   aws ec2 create-security-group \
     --group-name webapp-app-sg \
     --description "Application servers" \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=security-group,Tags=[
       {Key=Name,Value=webapp-app-sg}
     ]' \
     --profile admin

   export APP_SG=sg-xxxxxxxxx

   # Allow SSH from bastion only
   aws ec2 authorize-security-group-ingress \
     --group-id $APP_SG \
     --protocol tcp \
     --port 22 \
     --source-group $BASTION_SG \
     --profile admin

   # Allow HTTP from ALB (will create ALB SG later)
   aws ec2 authorize-security-group-ingress \
     --group-id $APP_SG \
     --protocol tcp \
     --port 80 \
     --source-group $ALB_SG \
     --profile admin
   ```

3. **Create ALB Security Group**
   ```bash
   aws ec2 create-security-group \
     --group-name webapp-alb-sg \
     --description "Application Load Balancer" \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=security-group,Tags=[
       {Key=Name,Value=webapp-alb-sg}
     ]' \
     --profile admin

   export ALB_SG=sg-xxxxxxxxx

   # Allow HTTP from anywhere
   aws ec2 authorize-security-group-ingress \
     --group-id $ALB_SG \
     --protocol tcp \
     --port 80 \
     --cidr 0.0.0.0/0 \
     --profile admin

   # Allow HTTPS from anywhere
   aws ec2 authorize-security-group-ingress \
     --group-id $ALB_SG \
     --protocol tcp \
     --port 443 \
     --cidr 0.0.0.0/0 \
     --profile admin
   ```

4. **Create RDS Security Group**
   ```bash
   aws ec2 create-security-group \
     --group-name webapp-rds-sg \
     --description "RDS database" \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=security-group,Tags=[
       {Key=Name,Value=webapp-rds-sg}
     ]' \
     --profile admin

   export RDS_SG=sg-xxxxxxxxx

   # Allow PostgreSQL from app servers only
   aws ec2 authorize-security-group-ingress \
     --group-id $RDS_SG \
     --protocol tcp \
     --port 5432 \
     --source-group $APP_SG \
     --profile admin
   ```

5. **Understand Security Group Statefulness**
   ```bash
   # Security Groups are STATEFUL
   # If you allow inbound, response is automatically allowed outbound
   # No need for explicit outbound rules

   # Example:
   # Inbound: Allow 80 from ALB
   # Outbound: Auto-allowed (return traffic)
   ```

**Success Criteria:**
- 4 security groups created
- Each follows least-privilege principle
- Bastion only accessible from your IP
- App servers only from bastion (SSH) and ALB (HTTP)
- RDS only from app servers
- Can explain why outbound rules aren't needed

**Challenge Questions:**
- What happens if you delete bastion SG rule?
- Why reference SG IDs instead of CIDR blocks?
- What's the default outbound rule?

---

### Part 6: Network ACLs (Day 7-8)

**Tasks:**

1. **Understand NACL vs Security Group**
   ```
   Security Group:
   - Instance level
   - Stateful
   - Allow rules only
   - Evaluates all rules

   NACL:
   - Subnet level
   - Stateless
   - Allow + deny rules
   - Evaluates rules in order
   - Must allow both inbound AND outbound
   ```

2. **Create Custom NACL for Public Subnets**
   ```bash
   aws ec2 create-network-acl \
     --vpc-id $VPC_ID \
     --tag-specifications 'ResourceType=network-acl,Tags=[
       {Key=Name,Value=webapp-public-nacl}
     ]' \
     --profile admin

   export PUBLIC_NACL=acl-xxxxxxxxx
   ```

3. **Add NACL Rules**
   ```bash
   # Inbound rules

   # Rule 100: Allow HTTP from anywhere
   aws ec2 create-network-acl-entry \
     --network-acl-id $PUBLIC_NACL \
     --ingress \
     --rule-number 100 \
     --protocol tcp \
     --port-range From=80,To=80 \
     --cidr-block 0.0.0.0/0 \
     --rule-action allow \
     --profile admin

   # Rule 110: Allow HTTPS
   aws ec2 create-network-acl-entry \
     --network-acl-id $PUBLIC_NACL \
     --ingress \
     --rule-number 110 \
     --protocol tcp \
     --port-range From=443,To=443 \
     --cidr-block 0.0.0.0/0 \
     --rule-action allow \
     --profile admin

   # Rule 120: Allow SSH from your IP
   aws ec2 create-network-acl-entry \
     --network-acl-id $PUBLIC_NACL \
     --ingress \
     --rule-number 120 \
     --protocol tcp \
     --port-range From=22,To=22 \
     --cidr-block $MY_IP/32 \
     --rule-action allow \
     --profile admin

   # Rule 130: Allow ephemeral ports (return traffic)
   aws ec2 create-network-acl-entry \
     --network-acl-id $PUBLIC_NACL \
     --ingress \
     --rule-number 130 \
     --protocol tcp \
     --port-range From=1024,To=65535 \
     --cidr-block 0.0.0.0/0 \
     --rule-action allow \
     --profile admin

   # Outbound rules (required because stateless!)

   # Rule 100: Allow all outbound
   aws ec2 create-network-acl-entry \
     --network-acl-id $PUBLIC_NACL \
     --egress \
     --rule-number 100 \
     --protocol -1 \
     --cidr-block 0.0.0.0/0 \
     --rule-action allow \
     --profile admin
   ```

4. **Associate NACL with Subnets**
   ```bash
   # Associate with all public subnets
   aws ec2 replace-network-acl-association \
     --association-id aclassoc-xxxxxxxxx \
     --network-acl-id $PUBLIC_NACL \
     --profile admin
   ```

5. **Test NACL Blocking**
   ```bash
   # Add deny rule for specific IP
   aws ec2 create-network-acl-entry \
     --network-acl-id $PUBLIC_NACL \
     --ingress \
     --rule-number 10 \
     --protocol tcp \
     --port-range From=22,To=22 \
     --cidr-block 1.2.3.4/32 \
     --rule-action deny \
     --profile admin

   # Rules evaluated in order
   # Rule 10 (deny) is checked before Rule 120 (allow)
   ```

**Success Criteria:**
- Understand stateless nature of NACLs
- Can explain ephemeral port requirement
- Properly configured inbound and outbound rules
- Can use NACLs for subnet-level blocking

---

### Part 7: Launch Test Infrastructure (Day 8-9)

**Tasks:**

1. **Launch Bastion Host**
   ```bash
   # Get latest Amazon Linux 2 AMI
   AMI_ID=$(aws ec2 describe-images \
     --owners amazon \
     --filters "Name=name,Values=amzn2-ami-hvm-*-x86_64-gp2" \
     --query 'sort_by(Images, &CreationDate)[-1].ImageId' \
     --output text \
     --profile admin)

   # Launch in public subnet
   aws ec2 run-instances \
     --image-id $AMI_ID \
     --instance-type t2.micro \
     --key-name your-key-pair \
     --security-group-ids $BASTION_SG \
     --subnet-id $PUBLIC_SUBNET_1A \
     --tag-specifications 'ResourceType=instance,Tags=[
       {Key=Name,Value=webapp-bastion},
       {Key=Role,Value=Bastion}
     ]' \
     --profile admin

   export BASTION_INSTANCE=i-xxxxxxxxx
   ```

2. **Launch App Servers in Private Subnets**
   ```bash
   # Create user data script for simple web server
   cat > userdata.sh <<'EOF'
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd

   # Get instance metadata
   TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
   INSTANCE_ID=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id)
   AZ=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone)

   # Create simple HTML page
   cat > /var/www/html/index.html <<HTML
   <html>
   <body>
     <h1>WebApp Server</h1>
     <p>Instance ID: $INSTANCE_ID</p>
     <p>Availability Zone: $AZ</p>
   </body>
   </html>
   HTML
   EOF

   # Launch in private subnet 1a
   aws ec2 run-instances \
     --image-id $AMI_ID \
     --instance-type t2.micro \
     --key-name your-key-pair \
     --security-group-ids $APP_SG \
     --subnet-id $PRIVATE_SUBNET_1A \
     --user-data file://userdata.sh \
     --tag-specifications 'ResourceType=instance,Tags=[
       {Key=Name,Value=webapp-app-1a},
       {Key=Role,Value=AppServer},
       {Key=AZ,Value=us-east-1a}
     ]' \
     --profile admin

   # Repeat for private subnets 1b and 1c
   ```

3. **Test Bastion to App Server Connection**
   ```bash
   # SSH to bastion
   ssh -i your-key.pem ec2-user@<bastion-public-ip>

   # From bastion, SSH to app server (copy key to bastion first)
   ssh ec2-user@<app-private-ip>

   # From app server, test internet access
   curl google.com
   # Should work via NAT Gateway

   # Test metadata service
   curl http://169.254.169.254/latest/meta-data/instance-id
   ```

**Success Criteria:**
- Bastion accessible from your IP only
- Can SSH from bastion to app servers
- App servers have no public IP
- App servers can reach internet via NAT
- Web server running on port 80

---

### Part 8: Traffic Flow Debugging (Day 9-10)

**Tasks:**

1. **Trace Traffic Flow Manually**

   **Internet → ALB → App Server:**
   ```
   1. User browser → DNS lookup
   2. → Internet
   3. → ALB (public subnet)
   4. → ALB security group check (allow 80/443?)
   5. → Target group health check
   6. → App server (private subnet)
   7. → App SG check (allow 80 from ALB SG?)
   8. → Application responds
   9. → Return path (stateful, auto-allowed)
   ```

   **App Server → Internet (via NAT):**
   ```
   1. App server → yum update
   2. → Private route table lookup
   3. → 0.0.0.0/0 → NAT Gateway
   4. → NAT in public subnet
   5. → Public route table lookup
   6. → 0.0.0.0/0 → IGW
   7. → Internet
   8. → Return traffic via NAT (tracks connections)
   ```

2. **Simulate Connection Failures**

   **Scenario 1: Remove IGW**
   ```bash
   # Detach IGW
   aws ec2 detach-internet-gateway \
     --vpc-id $VPC_ID \
     --internet-gateway-id $IGW_ID \
     --profile admin

   # Try to access bastion
   ssh ec2-user@<bastion-ip>
   # Result: Connection timeout (no route to internet)

   # Try from app server to internet
   # Result: Connection timeout (NAT can't reach internet)

   # Fix: Reattach IGW
   aws ec2 attach-internet-gateway \
     --vpc-id $VPC_ID \
     --internet-gateway-id $IGW_ID \
     --profile admin
   ```

   **Scenario 2: Remove NAT Route**
   ```bash
   # Delete NAT route from private route table
   aws ec2 delete-route \
     --route-table-id $PRIVATE_RT_1A \
     --destination-cidr-block 0.0.0.0/0 \
     --profile admin

   # From app server
   curl google.com
   # Result: Connection timeout (no route to internet)

   # Fix: Add route back
   aws ec2 create-route \
     --route-table-id $PRIVATE_RT_1A \
     --destination-cidr-block 0.0.0.0/0 \
     --nat-gateway-id $NAT_GW_1A \
     --profile admin
   ```

   **Scenario 3: Break Security Group**
   ```bash
   # Remove SSH rule from bastion SG
   aws ec2 revoke-security-group-ingress \
     --group-id $BASTION_SG \
     --protocol tcp \
     --port 22 \
     --cidr $MY_IP/32 \
     --profile admin

   # Try SSH
   ssh ec2-user@<bastion-ip>
   # Result: Connection timeout (SG blocks)

   # Fix: Add back
   aws ec2 authorize-security-group-ingress \
     --group-id $BASTION_SG \
     --protocol tcp \
     --port 22 \
     --cidr $MY_IP/32 \
     --profile admin
   ```

3. **Understand Error Types**
   ```
   Connection Timeout:
   - Security group blocking
   - NACL blocking
   - No route in route table
   - Instance not running
   - No IGW/NAT

   Connection Refused:
   - Application not listening on port
   - Firewall on instance blocking
   - Service not started

   DNS Resolution Failed:
   - VPC DNS not enabled
   - Wrong DNS server
   - Domain doesn't exist
   ```

4. **Use VPC Flow Logs for Debugging**
   ```bash
   # Create CloudWatch log group
   aws logs create-log-group \
     --log-group-name /aws/vpc/flowlogs \
     --profile admin

   # Create IAM role for flow logs (covered in Phase 0)
   # Attach policy: AmazonVPCFlowLogsPolicy

   # Enable flow logs on VPC
   aws ec2 create-flow-logs \
     --resource-type VPC \
     --resource-ids $VPC_ID \
     --traffic-type ALL \
     --log-destination-type cloud-watch-logs \
     --log-group-name /aws/vpc/flowlogs \
     --deliver-logs-permission-arn arn:aws:iam::ACCOUNT:role/flowlogsRole \
     --profile admin

   # View logs in CloudWatch
   # Look for ACCEPT vs REJECT
   ```

**Success Criteria:**
- Can trace any traffic flow on paper
- Successfully broke and fixed 3+ scenarios
- Understand timeout vs refused vs DNS errors
- Can use flow logs for debugging

---

### Part 9: Cost Optimization & Cleanup (Day 10-11)

**Tasks:**

1. **Calculate Infrastructure Costs**
   ```
   Monthly costs:

   NAT Gateways:
   - 3 × $0.045/hour × 730 hours = $98.55
   - Data processing: ~$0.045/GB

   EC2 Instances:
   - 3 app servers (t2.micro): 3 × $8.47 = $25.41
   - 1 bastion (t2.micro): $8.47

   Elastic IPs:
   - 3 × $0.005/hour (if not attached) = negligible
   - Free if attached to running instance

   Data Transfer:
   - Intra-AZ: Free
   - Inter-AZ: $0.01/GB
   - To internet: $0.09/GB

   Total: ~$132/month
   ```

2. **Cost Optimization Strategies**
   ```bash
   # Option 1: Single NAT Gateway (not HA)
   # Savings: $66/month
   # Risk: Single point of failure

   # Option 2: NAT Instance instead of NAT Gateway
   # t3.micro: ~$8/month vs $32/month per NAT
   # Savings: $72/month (but requires management)

   # Option 3: Stop instances when not in use
   # Savings: ~$34/month if stopped 12h/day

   # Option 4: Use VPC endpoints for AWS services
   # Avoid NAT costs for S3/DynamoDB traffic
   ```

3. **Set Up Cost Alerts**
   ```bash
   # Create budget
   aws budgets create-budget \
     --account-id YOUR_ACCOUNT_ID \
     --budget file://budget.json \
     --notifications-with-subscribers file://notifications.json \
     --profile admin
   ```

4. **Clean Up Resources (Important!)**
   ```bash
   # Delete EC2 instances
   aws ec2 terminate-instances \
     --instance-ids $BASTION_INSTANCE $APP_INSTANCE_1A $APP_INSTANCE_1B \
     --profile admin

   # Wait for termination
   aws ec2 wait instance-terminated \
     --instance-ids $BASTION_INSTANCE \
     --profile admin

   # Delete NAT Gateways (must wait for deletion)
   aws ec2 delete-nat-gateway \
     --nat-gateway-id $NAT_GW_1A \
     --profile admin

   # Wait 5-10 minutes

   # Release Elastic IPs
   aws ec2 release-address \
     --allocation-id $NAT_EIP_1A \
     --profile admin

   # Delete Internet Gateway
   aws ec2 detach-internet-gateway \
     --vpc-id $VPC_ID \
     --internet-gateway-id $IGW_ID \
     --profile admin

   aws ec2 delete-internet-gateway \
     --internet-gateway-id $IGW_ID \
     --profile admin

   # Delete route tables (non-default)
   aws ec2 delete-route-table \
     --route-table-id $PUBLIC_RT_ID \
     --profile admin

   # Delete subnets
   aws ec2 delete-subnet \
     --subnet-id $PUBLIC_SUBNET_1A \
     --profile admin

   # Delete security groups (delete in reverse dependency order)
   aws ec2 delete-security-group \
     --group-id $APP_SG \
     --profile admin

   # Finally, delete VPC
   aws ec2 delete-vpc \
     --vpc-id $VPC_ID \
     --profile admin
   ```

**Success Criteria:**
- Understand all cost components
- Know optimization strategies
- Successfully cleaned up all resources
- Billing alerts configured
- No unexpected charges

---

### Part 10: Final Capstone Challenge (Day 11-14)

**Build from scratch without notes:**

**Time limit: 2 hours**

**Requirements:**

1. Create VPC (10.1.0.0/16)
2. Create 2 public subnets (different AZs)
3. Create 2 private subnets (different AZs)
4. Internet Gateway + routing
5. NAT Gateway (one is fine)
6. Proper security groups
7. Launch bastion in public
8. Launch web server in private
9. Test connectivity
10. Document architecture
11. Clean up everything

**Bonus Challenges:**

1. Implement bastion-less access using Session Manager
2. Set up VPC peering with another VPC
3. Configure VPC endpoints for S3
4. Implement VPN connection simulation
5. Set up Transit Gateway (advanced)

**Success Criteria:**
- Complete under 2 hours
- All connectivity working
- No documentation needed
- Can explain every decision
- Clean deletion (no errors)

---

## 🎓 Knowledge Check Questions

### VPC & CIDR
1. How many IP addresses in a /20 subnet?
2. Why does AWS reserve 5 IPs per subnet?
3. Can you change VPC CIDR after creation?
4. What's the largest and smallest VPC CIDR?
5. How do you calculate usable IPs?

### Routing
1. What's the difference between local and default routes?
2. Why must NAT be in public subnet?
3. What happens if two routes overlap?
4. Can you have multiple IGWs per VPC?
5. Why do we need separate route tables per AZ?

### Security
1. Security Group vs NACL - 5 key differences?
2. Why are ephemeral ports important for NACLs?
3. Can security groups span VPCs?
4. What's the default SG behavior?
5. How does source SG reference work?

### High Availability
1. Why deploy NAT Gateway per AZ?
2. What happens if an AZ fails?
3. How does ALB distribute traffic?
4. Why minimum 2 subnets for ALB?
5. What's the cost of HA?

### Debugging
1. Timeout vs connection refused - what's the difference?
2. How to debug "no route to host"?
3. What do VPC Flow Logs show?
4. How to test if security group is the issue?
5. Order of evaluation: route table, NACL, SG?

---

## 🎯 Success Metrics

You've completed Phase 1 when you can:

- ✅ Design VPC architecture on paper in 15 minutes
- ✅ Calculate CIDR ranges without calculator
- ✅ Explain every routing decision
- ✅ Debug connectivity issues in minutes
- ✅ Understand NAT Gateway costs completely
- ✅ Trace traffic flow through all layers
- ✅ Build production VPC from memory
- ✅ Know when to use SG vs NACL
- ✅ Implement multi-AZ HA architecture
- ✅ Complete capstone under 2 hours

---

## 📊 Project Deliverables

Document your work:

1. **Network Diagram** (with CIDR, routes, SG rules)
2. **Route Table Documentation** (all routes explained)
3. **Security Group Matrix** (who can talk to whom)
4. **Cost Analysis** (actual AWS bill breakdown)
5. **Traffic Flow Diagrams** (5+ scenarios)
6. **Debugging Journal** (what broke, how you fixed)
7. **CLI Command Library** (your go-to commands)
8. **Knowledge Check Answers** (written explanations)

---

## 🚀 Extensions (Optional)

1. **Add Application Load Balancer**
   - Target groups
   - Health checks
   - SSL/TLS termination

2. **Implement Auto Scaling**
   - Launch templates
   - Scaling policies
   - CloudWatch alarms

3. **VPC Peering**
   - Connect to another VPC
   - Update route tables
   - Test connectivity

4. **VPC Endpoints**
   - S3 gateway endpoint
   - DynamoDB endpoint
   - Interface endpoints

5. **Site-to-Site VPN**
   - Virtual private gateway
   - Customer gateway
   - VPN connection

6. **Transit Gateway**
   - Hub-and-spoke model
   - Multiple VPC connections
   - Centralized routing

---

## 💡 Pro Tips

1. **Always tag route tables** - You'll forget which is which
2. **Name everything clearly** - webapp-public-1a is better than subnet-1
3. **Document CIDR decisions** - Future you needs to understand
4. **Test with ping/curl first** - Before deploying real apps
5. **Delete NAT when learning** - Don't pay $3+/day
6. **Use Session Manager** - Cheaper than bastion hosts
7. **Draw before building** - Paper diagrams save time
8. **One NAT for learning** - Multi-AZ NAT for production
9. **Enable flow logs early** - Debugging is easier
10. **Automate cleanup** - Script the deletion order

---

## 🎬 Next Steps After Completion

1. **Review Phase 1 Checklist** - All items complete
2. **Pass Knowledge Check** - 90%+ score
3. **Complete Capstone** - Under 2 hours
4. **Document Lessons Learned**
5. **Move to Phase 2** - Compute (EC2, ASG, ALB in depth)
6. **Or** - Begin Terraform version of this project

---

## 📚 Additional Resources

- [VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)
- [CIDR Calculator](https://www.ipaddressguide.com/cidr)
- [VPC Pricing](https://aws.amazon.com/vpc/pricing/)
- [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
- [Security Group Rules Reference](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-group-rules-reference.html)

---

## ⚠️ Common Pitfalls

1. **Forgetting NAT costs** - $3+/day adds up fast
2. **Wrong subnet for NAT** - Must be public
3. **Missing ephemeral ports in NACL** - Return traffic blocked
4. **Not enough IPs in subnet** - /28 too small for production
5. **Single AZ deployment** - No high availability
6. **Overlapping CIDRs** - Can't peer VPCs later
7. **Default VPC confusion** - Always use custom VPC
8. **Not testing before deletion** - Verify connectivity works
9. **Deleting in wrong order** - Dependencies cause errors
10. **Ignoring flow logs** - Missing debugging gold mine

---

**Remember:** Networking is the foundation for everything in AWS. ECS, EKS, RDS, Lambda - all depend on VPC knowledge. Master this and Phase 2+ becomes much easier.

**When Phase 1 feels natural, you're ready for Phase 2 (Compute + Load Balancing).**

Good luck! 🚀
