Let's break down how to "lock root credentials in a password manager" and "document root account email" within the context of AWS Cloud.

### Locking Root Credentials in a Password Manager

This is a crucial security step. You should **never** store your root user's password directly in an unencrypted or easily accessible format. A password manager provides a secure, encrypted vault for this information.

Here's how to do it:

1.  **Choose a Reputable Password Manager:** There are many excellent options available, both free and paid. Popular choices include:
    *   LastPass
    *   1Password
    *   Bitwarden
    *   KeePass (open-source, requires manual syncing)

2.  **Generate a Strong, Unique Password for Your Root Account:**
    *   When you first set up your AWS root account, you'll create a password. If you haven't yet, or if you want to update it, ensure it's very strong.
    *   Use a combination of uppercase and lowercase letters, numbers, and symbols.
    *   Aim for at least 16 characters, but longer is better.
    *   **Crucially, do not reuse this password from any other account.**

3.  **Store the Root Account Password in Your Password Manager:**
    *   Open your chosen password manager.
    *   Create a new entry specifically for your AWS Root Account.
    *   **Title:** Something clear like "AWS Root Account" or "Amazon Web Services Root User".
    *   **Username/Email:** This will be the email address associated with your AWS root account (which you'll document separately).
    *   **Password:** Copy and paste the strong password you generated/set for your root account into the password field.
    *   **URL (Optional but Recommended):** Add `https://aws.amazon.com/console/` for easy access.
    *   **Notes (Optional but Recommended):** You can add details like the date the password was last changed, or any other relevant information.

4.  **Secure Your Password Manager:**
    *   Ensure your password manager itself is protected by a very strong, unique master password.
    *   Enable MFA on your password manager account if it offers the option.

**Why is this important?**
*   **Security:** Keeps your root password encrypted and protected from unauthorized access.
*   **Convenience:** You don't need to remember a complex password; the password manager does it for you.
*   **Best Practice:** Part of a strong security posture.

### Documenting Root Account Email

This is straightforward but essential for recovery and administrative purposes.

1.  **Identify the Root Account Email:**
    *   The root account email is the email address you used to initially sign up for your AWS account.
    *   You can find this by logging into the AWS Management Console as the root user (if you still have access) and navigating to the top right corner where your account name is displayed. Click on your account name, and then "My Account". The email address will be listed there.

2.  **Choose a Secure Documentation Method:**
    *   **Password Manager (Recommended):** The easiest and most secure place to document this is within the same password manager entry where you stored the root account password. Use the "Username/Email" field or add it to the "Notes" section.
    *   **Secure Digital Document:** If you use a secure, encrypted document management system (e.g., an encrypted file on a secure drive, a secure note-taking app with encryption), you can store it there.
    *   **Physical, Secure Location (Least Recommended for accessibility, but an option):** If you absolutely need a physical copy, ensure it's stored in a locked safe or secure location. However, digital storage in an encrypted password manager is generally preferred for accessibility and searchability.

**What to Document:**

*   **AWS Root Account Email Address:** The full email address.
*   **AWS Account ID (Optional but Highly Recommended):** This is a 12-digit number that uniquely identifies your AWS account. You can find it in the top right corner of the AWS Management Console after logging in, next to your account name.

**Why is this important?**
*   **Account Recovery:** If you ever lose access to your root account, knowing the associated email is critical for recovery processes.
*   **Administrative Tasks:** Some AWS support requests or integrations might require you to provide your root account email or account ID.
*   **Security Auditing:** It helps maintain a clear record of who owns the root account.

**In summary, for your Day 1 tasks:**

1.  **Secure the Root Account:** This is an ongoing process, but initially, it means setting a strong password and enabling MFA.
2.  **Enable MFA on Root:** Follow AWS documentation for this. It typically involves using a virtual MFA device (like Google Authenticator or Authy) or a hardware MFA device.
3.  **Lock root credentials in password manager:** Store the root email and its strong, unique password in a reputable, MFA-protected password manager.
4.  **Document root account email:** The best place for this is usually within the same password manager entry for your AWS root account.

By following these steps, you significantly enhance the security of your AWS environment from day one!

### Add View Billing Permissions
Let's break down the concepts of Roles, Permissions, and Policies in AWS IAM, and then apply that knowledge to granting specific access to your `admin-abhishant` user for Cost Explorer and Billing.

---

### Understanding AWS IAM: Roles, Permissions, and Policies

AWS Identity and Access Management (IAM) is the service that lets you securely control access to AWS resources.

#### 1. Policy (The "What you can do")

*   **Definition:** A policy is a document that formally states a set of permissions. It defines *what actions* are allowed or denied on *which resources* under *what conditions*.
*   **Format:** Policies are written in JSON.
*   **Types:**
    *   **Managed Policies:**
        *   **AWS Managed Policies:** Created and managed by AWS (e.g., `AmazonS3FullAccess`, `AdministratorAccess`). These are convenient but often grant more permissions than strictly necessary.
        *   **Customer Managed Policies:** Created and managed by you. These allow for fine-grained control and adherence to the principle of least privilege.
    *   **Inline Policies:** Embedded directly into an IAM identity (user, group, role). They are deleted if the identity is deleted.

*   **Example Policy Structure (simplified):**
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "s3:GetObject",
                    "s3:ListBucket"
                ],
                "Resource": [
                    "arn:aws:s3:::my-example-bucket",
                    "arn:aws:s3:::my-example-bucket/*"
                ]
            },
            {
                "Effect": "Deny",
                "Action": "s3:DeleteObject",
                "Resource": "arn:aws:s3:::my-example-bucket/critical-data/*"
            }
        ]
    }
    ```
    *   `Effect`: `Allow` or `Deny`.
    *   `Action`: The specific API calls (e.g., `s3:GetObject`).
    *   `Resource`: The ARN (Amazon Resource Name) of the resource the action applies to.
    *   `Condition` (optional): Criteria that must be met for the policy to take effect.

#### 2. Permission (The "Ability to do something")

*   **Definition:** A permission is the specific authorization to perform an action on a resource. It's the outcome of an applied policy.
*   **Relationship to Policy:** Policies *define* permissions. When a policy is attached to an identity, that identity *gains* the permissions specified in the policy.
*   **Granularity:** Permissions can be very broad (e.g., "full access to S3") or very specific (e.g., "read-only access to a single file in a specific S3 bucket only from a specific IP address").

#### 3. Role (The "Who or What can assume these permissions")

*   **Definition:** An IAM role is an identity that you can create in your account that has specific permissions. It is similar to an IAM user, but it is not associated with a specific person. Instead, it is intended to be assumable by anyone or anything that needs to assume it.
*   **Trust Policy:** Every role has a "Trust Policy" that defines *who* (users, other AWS accounts, AWS services like EC2, Lambda) is allowed to assume that role.
*   **Use Cases:**
    *   **Delegating access to users in other AWS accounts.**
    *   **Granting permissions to AWS services** (e.g., an EC2 instance needing to access S3).
    *   **Granting temporary elevated permissions to IAM users** within the same account.

#### Flow: How they work together

1.  **You define a Policy:** You write a JSON document specifying what actions are allowed/denied on what resources.
2.  **You attach the Policy to an Identity:**
    *   **To a User:** The user directly gains those permissions.
    *   **To a Group:** All users in that group gain those permissions.
    *   **To a Role:** Anyone or anything that assumes that role gains those permissions.
3.  **When an identity (user, service, or assumed role) tries to perform an action:** AWS evaluates all policies attached to that identity (and any groups it belongs to, or roles it has assumed) to determine if the action is allowed or denied.

---

### Assigning Cost Explorer and Billing Management Access to `admin-abhishant`

Now, let's apply this to your user. `admin-abhishant` is an IAM user. We will attach policies directly to this user (or, preferably, to a group that `admin-abhishant` is a member of, for better management).

**Goal:** Grant `admin-abhishant` permission to:
*   View and interact with AWS Cost Explorer.
*   View billing information.

#### Step-by-Step Instructions:

1.  **Login to AWS Management Console:** Log in as your AWS Root user or an IAM user with sufficient permissions to manage IAM (e.g., a user with `IAMFullAccess`).

2.  **Navigate to IAM:**
    *   Search for "IAM" in the search bar or find it under "Security, Identity, & Compliance".

3.  **Find the User `admin-abhishant`:**
    *   In the IAM dashboard, click on "Users" in the left navigation pane.
    *   Search for and click on `admin-abhishant`.

4.  **Attach Policies:**
    *   On the user's details page, go to the "Permissions" tab.
    *   Click on "Add permissions".
    *   Select "Attach existing policies directly" (or "Add user to group" if you manage permissions via groups, which is recommended).

5.  **Search for and Select Policies:**
    You'll need two main policies for this access:

    a.  **For Cost Explorer Access:**
        *   Search for: `AWSCostExplorerReadOnlyAccess`
        *   This policy grants read-only access to AWS Cost Explorer data. If `admin-abhishant` needs to save reports or budgets, you might consider `AWSCostExplorerServiceFullAccess` (but start with read-only as per least privilege).

    b.  **For Billing Management Access:**
        *   Search for: `ViewOnlyAccess`
        *   This AWS managed policy provides read-only access to most billing and cost management functionalities, including billing details, invoices, and payment history.
        *   **Important Note for Billing Access:** By default, IAM users do *not* have access to the Billing Console, even with policies like `ViewOnlyAccess`. You *must* explicitly enable this.

6.  **Enable IAM User Access to Billing Information (Crucial Step!):**
    This is a common pitfall. Even with the `ViewOnlyAccess` policy, IAM users cannot see billing information unless this setting is enabled.

    *   While still in the IAM dashboard, click on "Account settings" in the left navigation pane.
    *   Scroll down to "IAM user and role access to Billing information".
    *   Click "Edit".
    *   Check the box for "Activate IAM access to the Billing console".
    *   Click "Update".

7.  **Review and Add Permissions:**
    *   After selecting both policies, click "Next: Review".
    *   Verify the policies you're attaching.
    *   Click "Add permissions".

#### Summary of Access Granted:

*   **`AWSCostExplorerReadOnlyAccess`**: Allows `admin-abhishant` to view and analyze cost and usage data within the AWS Cost Explorer service.
*   **`ViewOnlyAccess`**: Grants read-only access to various AWS services, including the billing console (once explicitly enabled in Account Settings). This allows `admin-abhishant` to see invoices, payment history, cost reports, etc.

Now, `admin-abhishant` should be able to log in to the AWS Management Console and access both the Cost Explorer and Billing dashboards.

---

**Best Practice Note:** While attaching policies directly to users works, a more scalable and manageable approach is to create **IAM Groups** (e.g., `FinanceTeam`, `CostAnalysts`), attach the necessary policies to these groups, and then add users like `admin-abhishant` to the appropriate groups. This simplifies management as you only need to update group policies, and all users in that group inherit the changes.