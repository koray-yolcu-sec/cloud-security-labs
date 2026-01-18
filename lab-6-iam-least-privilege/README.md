# 🔐 Lab 6 – AWS IAM Basics & Least Privilege Access Control

> **Category:** Cloud Security  
> **Platform:** AWS  
> **Focus:** IAM, Least Privilege, Access Control  
> **Level:** Intermediate  

This lab focuses on implementing **least-privilege access control** in AWS using IAM users, groups, and custom policies.  
The goal is to ensure that identities are granted **only the permissions they strictly need**, and nothing more.

---

## 🎯 Objectives

* **Understand** AWS IAM core components (users, groups, policies)
* **Create** a least-privilege IAM policy
* **Assign** permissions via groups instead of directly to users
* **Verify** access restrictions using real authorization failures
* **Demonstrate** AccessDenied behavior as proof of enforcement

---

## 🧪 Environment

| Item | Details |
| :--- | :--- |
| **Cloud Provider** | AWS |
| **Service** | IAM (Global) |
| **Account Type** | Personal / Free Tier |
| **Region** | Global (IAM is regionless) |

---

## 🧱 Step 1 – Create Least-Privilege IAM Group

A new IAM group was created to represent a **restricted role**:

* **Group name:** `lab-6-least-privilege-group`

The group itself initially had **no permissions attached**, following the default-deny principle.

---

## 📜 Step 2 – Create Custom Read-Only IAM Policy

A **customer-managed IAM policy** was created to allow **read-only access to EC2 resources only**.

* **Policy name:** `lab-6-ec2-readonly-policy`

### Policy Definition:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeImages",
        "ec2:DescribeKeyPairs"
      ],
      "Resource": "*"
    }
  ]
}
```

### This policy does not allow:

- Instance creation or termination

- Security group modification

- Any write-level EC2 action

---

## 👤 Step 3 – Create IAM User (No Console Access)

An IAM user was created for testing purposes:

User name: `lab6-readonly-user`

Security Decisions:

❌ Console access disabled

❌ No permissions attached directly

✅ User added to the least-privilege group only

This ensures permissions are group-driven, not user-specific.

---

## 🔗 Step 4 – Attach Policy via Group
The custom policy was attached to the group to enforce centralized access control:

- `lab-6-least-privilege-group`

   - └── `lab-6-ec2-readonly-policy`

       - └── `lab6-readonly-user`

This aligns with real-world IAM best practices for scalable permission management.

---

## 🧪 Step 5 – Permission Validation (Expected Failures)
To validate least privilege, actions outside the policy scope were attempted.

Example: Unauthorized Action Attempt Attempting to perform a write-level EC2 action resulted in: 

|`AccessDenied: User is not authorized to perform this action`|

This confirms:

- The policy is active and functional.

- No implicit permissions exist.

- Default-deny is enforced correctly.

---

## 🔍 Observations
### Explicit Evaluation: 
IAM policies are evaluated strictly; if it's not explicitly allowed, it's denied.

### Scalability: 
Group-based permissions scale significantly better than user-based permissions.

### Blast Radius: 
Least privilege significantly reduces the potential impact of a compromised identity.

### Verification: 
`AccessDenied` errors are valuable verification signals rather than system failures.

---

## ✅ Results
- [x] IAM user has only read-only EC2 permissions.

- [x] No console access granted.

- [x] No write or admin actions possible.

- [x] Permissions enforced strictly through group membership.

- [x] Least-privilege principle validated through real denial events.

---

## ✅ Outcome
This lab demonstrates practical IAM security fundamentals: 
identity separation, least-privilege policy design, and access validation through real authorization failures.
These concepts are foundational for cloud security, DevOps security, and infrastructure hardening roles.
