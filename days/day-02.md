# Day 2 - IAM (Identity and Access Management)

## Topics covered
- Understanding IAM components
- Real-life IAM scenario
- IAM in AWS context
- Creating an IAM user (demo)
- Authentication vs Authorization
- Attaching policies to users
- Using IAM user groups

## What I learned

### 1. Understanding IAM components
IAM (Identity and Access Management) controls **who** can access AWS and **what** they can do. Core components:
- **Users** — individual identities (a person or an application) with their own credentials.
- **Groups** — a collection of users; policies attached to a group apply to every user in it (easier to manage permissions at scale).
- **Roles** — a set of permissions that can be assumed temporarily by a user, an AWS service, or an external identity — no long-term credentials involved.
- **Policies** — JSON documents that define permissions (what actions are allowed/denied on which resources).

### 2. Real-life IAM scenario
Example: a company has a DevOps engineer, a developer, and a billing/finance person.
- The DevOps engineer needs access to EC2, S3, and deployment tools.
- The developer only needs access to specific S3 buckets and logs, not billing.
- The finance person only needs access to the Billing dashboard, nothing else.

Instead of giving everyone full (root-level) access, IAM lets you create separate users, group them by role (e.g., `DevOps-Group`, `Dev-Group`, `Finance-Group`), and attach only the policies each group actually needs. This is the **principle of least privilege** — give only the minimum permissions required to do the job.

### 3. IAM in AWS context
- IAM is **global** — it's not tied to a specific region.
- IAM is **free** to use.
- The **root user** (created when you set up the AWS account) has unrestricted access to everything, including billing — it should not be used for daily tasks.
- Every API call/console action in AWS is checked against IAM policies to decide allow or deny.

### 4. Creating an IAM user (demo)
Steps covered:
1. Go to IAM console → Users → "Create user"
2. Provide a username
3. Decide if the user needs AWS Management Console access (set a password) and/or programmatic access (access key/secret key for CLI/SDK)
4. Set permissions — either attach policies directly, add the user to a group, or copy permissions from an existing user
5. Review and create — download/save credentials securely (shown only once)

### 5. Authentication vs Authorization
| | Authentication | Authorization |
|---|---|---|
| Question answered | "Who are you?" | "What are you allowed to do?" |
| Mechanism in AWS | Username/password, access keys, MFA | IAM policies attached to users/groups/roles |
| Example | Logging into the AWS console with valid credentials | Being allowed (or denied) to launch an EC2 instance once logged in |

Authentication happens first (prove identity), authorization happens next (check permissions for the requested action).

### 6. Attaching policies to users
- Policies can be **attached directly to a user** (quick, but hard to manage at scale).
- Better practice: attach policies to a **group**, then add users to that group — permission changes apply to everyone in the group automatically.
- AWS provides **managed policies** (predefined by AWS, e.g., `AmazonS3ReadOnlyAccess`) and you can also write **custom/inline policies** for fine-grained control.
- Policies follow a JSON structure with `Effect` (Allow/Deny), `Action` (e.g., `s3:GetObject`), and `Resource` (which ARN it applies to).

### 7. Using IAM user groups
- A group is just a named collection of users sharing the same permission set.
- Create a group (e.g., `Developers`), attach the required policies to the group, then add users to it.
- If a new developer joins, just add them to the `Developers` group instead of re-attaching individual policies — much easier to maintain and audit.
- A user can belong to multiple groups and inherits the combined permissions of all of them.

## Hands-on / labs
- Created an IAM user (`meg_test`) via the IAM console
- Attached `AmazonS3FullAccess` directly to the user and tested S3 bucket listing
- Troubleshot a "you don't have permissions to list buckets" error — checked permissions boundary, group policies, and console session cache as possible causes
- Created an IAM group and attached a managed policy to it:
  1. IAM console → **IAM user groups** → **Create group**
  2. Enter a group name (e.g., `S3-Practice-Group`)
  3. Under **Attach permissions policies**, select the desired policy (e.g., `AmazonS3FullAccess`)
  4. Click **Create user group**
- Added the user to the group:
  1. Open the group → **Users** tab → **Add users**
  2. Select `meg_test` → **Add users**
  (or from the user's page: **Groups** tab → **Add user to groups** → select group)
- Verified on the user's **Permissions** tab that the policy now shows **"Attached via"** the group name instead of "Directly"
- Removed the policy that was attached directly to the user, so all permissions flow through the group (cleaner, matches the least-privilege/group-based pattern)

## Interview Q&A quick revision
- **Q: What is IAM and is it region-specific?** AWS's service for managing identities and permissions; it is global, not tied to any region.
- **Q: Difference between an IAM user, group, and role?** A user is an individual identity with its own credentials; a group is a collection of users sharing permissions; a role is a set of temporary permissions assumable by users, services, or external identities (no long-term credentials).
- **Q: Authentication vs authorization — what's the difference?** Authentication verifies identity ("who are you"); authorization determines permitted actions ("what can you do") via policies.
- **Q: Why attach policies to groups instead of individual users?** Easier management at scale — permission changes apply to every member automatically, reducing manual work and risk of inconsistent access.
- **Q: What is the principle of least privilege?** Grant only the minimum permissions a user/role needs to perform their job — nothing more.
- **Q: Should the root account be used for daily operations?** No — create IAM users/roles with scoped permissions and reserve root for account-level tasks only.
