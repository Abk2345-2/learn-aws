Below is a **Phase 1: AWS Networking – Deep Practice TODO List**, designed for **hands-on mastery**, not theory.

---

# ✅ Phase 1: AWS Networking — TODO Checklist

⏱️ Target time: **10–14 days**
🎯 Goal: *You can design, debug, and explain AWS networking without guessing*

---

## 🧠 A. Core Concepts (Understand Before Building)

☐ Understand **what a VPC really is**

* Logical network boundary (not hardware)
* Region-scoped
* Default vs custom VPC

☐ Learn **CIDR notation deeply**

* /16 vs /24 meaning
* How many IPs per subnet
* Reserved IPs in AWS (5 per subnet)

☐ Understand **Availability Zones**

* Why subnets are AZ-specific
* Why ALB spans AZs

☐ Know the difference between:

* Public subnet
* Private subnet
* Isolated subnet

📌 You should be able to *explain*:

> “Why does AWS recommend multiple AZs?”

---

## 🌐 B. VPC Creation (Hands-On)

☐ Create **custom VPC** via CLI or Console

* CIDR: `10.0.0.0/16`

☐ Create **2 public subnets**

* `10.0.1.0/24` (AZ-a)
* `10.0.2.0/24` (AZ-b)

☐ Create **2 private subnets**

* `10.0.11.0/24` (AZ-a)
* `10.0.12.0/24` (AZ-b)

☐ Verify subnet-to-AZ mapping

📌 Validation:

```bash
aws ec2 describe-subnets --filters Name=vpc-id,Values=<vpc-id>
```

---

## 🚪 C. Internet Gateway (IGW)

☐ Create Internet Gateway
☐ Attach IGW to VPC

☐ Understand:

* Why IGW is **stateless**
* Why private subnets shouldn’t route to IGW

☐ Create **public route table**

* `0.0.0.0/0 → IGW`

☐ Associate public subnets to this route table

📌 Test:

* Public subnet EC2 gets public IP
* Can ping 8.8.8.8

---

## 🔒 D. Private Subnets (No Internet Yet)

☐ Create **private route table**

* No default route to internet

☐ Associate private subnets

☐ Launch EC2 in private subnet

* No public IP

📌 Test:

* Cannot access internet
* Cannot be accessed from outside

---

## 🔁 E. NAT Gateway (This Explains AWS Billing 💸)

☐ Allocate Elastic IP
☐ Create NAT Gateway in **public subnet**

☐ Update private route table:

```
0.0.0.0/0 → NAT Gateway
```

☐ Understand:

* Why NAT must be in public subnet
* Why NAT costs money even when idle

📌 Test:

* Private EC2 can `curl google.com`
* Internet cannot access private EC2

---

## 🔐 F. Security Layers (Critical Difference)

☐ Learn **Security Groups**

* Stateful
* Allow rules only

☐ Learn **NACLs**

* Stateless
* Allow + deny
* Applied at subnet level

☐ Configure:

* SG allowing SSH only from your IP
* Backend SG allowing traffic only from frontend SG

📌 Explain:

> “Why does security group not need outbound rules?”

---

## 🧪 G. Traffic Flow Debugging (Must Do)

☐ Trace this flow manually:

```
Browser → ALB → EC2 → Internet
```

☐ Trace this:

```
Private EC2 → NAT → IGW → Internet
```

☐ Answer without notes:

* Why does `Connection refused` happen?
* What causes timeout vs refusal?
* What breaks if route table is wrong?

---

## 🔍 H. CLI Mastery (Required)

☐ Describe VPC:

```bash
aws ec2 describe-vpcs
```

☐ Describe route tables:

```bash
aws ec2 describe-route-tables
```

☐ Describe NAT Gateway:

```bash
aws ec2 describe-nat-gateways
```

☐ Delete NAT safely when done
☐ Release Elastic IP

📌 You should *never fear deleting networking*

---

## 💥 I. Break & Fix (Most Important Learning)

☐ Delete IGW → observe failures
☐ Remove NAT route → observe failures
☐ Remove SG rule → observe failures
☐ Fix everything back to working state

📌 If you can break & fix, you understand it.

---

## 📊 J. Cost Awareness (Mandatory)

☐ Identify **what costs money**

* NAT Gateway
* Elastic IP
* ALB
* Data transfer

☐ Set billing alert ($5 / day)

☐ Delete unused resources

---

## 🧠 K. Final Self-Test (You must pass)

You should confidently answer:

☐ Why private subnet cannot reach internet without NAT
☐ Why NAT must be in public subnet
☐ Why IGW alone is not enough
☐ Difference between SG and NACL
☐ Why EKS needs multiple subnets

---

## 🎯 Phase 1 Exit Criteria

✅ You can design a VPC on paper
✅ You can debug networking issues in minutes
✅ AWS networking no longer feels “magical”
✅ NAT Gateway billing makes sense

---

## 🔥 Want Next?

I can:

* Turn this into a **daily 14-day plan**
* Provide **Terraform version of this checklist**
* Create **real-world failure scenarios**
* Map this Phase 1 directly to **EKS internals**

Just tell me how you want to proceed 👌
