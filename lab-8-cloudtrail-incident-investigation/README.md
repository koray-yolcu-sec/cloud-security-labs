# 🔍 Lab 8 – CloudTrail Incident Investigation (IAM Programmatic Access)

> **Category:** Cloud Security  
> **Platform:** AWS  
> **Focus:** CloudTrail, IAM, Programmatic Access, Incident Investigation  
> **Level:** Intermediate  

This lab focuses on **investigating programmatic IAM activity using AWS CloudTrail**.  
The goal is to identify **who performed an API call**, **from where**, **using which credentials**, and **what level of access was enforced**.

Rather than hardening configurations, this lab shifts focus to **detection, attribution, and investigation** — core skills for cloud security and blue team roles.

---

## 🎯 Objectives

- **Detect** programmatic IAM access using CloudTrail
- **Attribute** API calls to a specific IAM user and access key
- **Identify** reconnaissance behavior via AWS STS
- **Validate** least-privilege enforcement through denied IAM actions
- **Practice** cloud incident investigation methodology

---

## 🧪 Environment

| Item | Details |
| :--- | :--- |
| **Cloud Provider** | AWS |
| **Services Used** | CloudTrail, IAM, STS |
| **Account Type** | Personal / Free Tier |
| **Region** | eu-north-1 |
| **Access Type** | Programmatic (AWS CLI) |

---

## 🧱 Scenario Overview

A restricted IAM user was created with **read-only EC2 permissions**.  
Programmatic credentials (access keys) were used to simulate **potential credential exposure**.

The investigation focuses on answering:

- Who used the credentials?
- From which IP address?
- What actions were attempted?
- Were permissions correctly enforced?

---

## 🔑 IAM Identity Context

- **IAM User:** `lab8-investigation-user`
- **Access Type:** Programmatic (Access Key)
- **Permissions:** Limited (Least Privilege enforced via IAM group)

No console access was enabled.

---

## 🕵️ Event 1 – Identity Reconnaissance (GetCallerIdentity)

The first observed event was a call to AWS STS to identify the active identity.

### CloudTrail Evidence

- **Event name:** `GetCallerIdentity`
- **Event source:** `sts.amazonaws.com`
- **Event type:** `AwsApiCall`
- **Access key used:** `AKIA************`
- **Source IP address:** External public IP
- **Region:** eu-north-1
- **Read-only:** true

### Key Finding

```json
"userIdentity": {
  "type": "IAMUser",
  "userName": "lab8-investigation-user",
  "accessKeyId": "AKIA************"
}
```

### Interpretation
This action represents initial reconnaissance.
In real-world cloud breaches, attackers commonly begin by validating which identity and permissions they currently possess.

---

## 🚫 Event 2 – Unauthorized IAM Action (ListUsers)
After identity confirmation, an IAM enumeration attempt was made.

### Action Attempted
```bash
aws iam list-users
```

---

## Result
- Outcome: AccessDenied
- Reason: IAM user lacks iam:ListUsers permission

## Security Significance
### This confirms:
- IAM permissions were correctly restricted
- No privilege escalation occurred
- Least-privilege access control functioned as intended

---

## ⏱️ Incident Timeline
- Programmatic credentials used from an external IP
- Identity validated via GetCallerIdentity
- Unauthorized IAM enumeration attempt
- Access denied by IAM policy
- Event recorded in CloudTrail management logs

---

## 🔍 Investigation Notes
- CloudTrail Event History may display root or service principals in summary views
- Definitive attribution must be performed using userIdentity and accessKeyId
- Programmatic access events are management events, not data plane actions

---

## 🧠 Lessons Learned
- Programmatic access is a high-risk attack vector
- CloudTrail is essential for identity attribution
- GetCallerIdentity is a strong indicator of reconnaissance activity
- Least privilege significantly reduces blast radius
- Detection is as important as prevention in cloud security

---

## 🌍 Real-World Relevance
This lab mirrors real cloud security incidents where:
IAM credentials are exposed
Attackers probe identity and permissions
Unauthorized actions are blocked but leave forensic evidence
The ability to detect, analyze, and explain these events is critical for:
Cloud Security Engineers
SOC / Blue Team roles
Incident Response teams

---

## ✅ Outcome
- Programmatic IAM access detected
- Actor attributed via access key
- Source IP identified
- Unauthorized action blocked
- CloudTrail investigation completed
