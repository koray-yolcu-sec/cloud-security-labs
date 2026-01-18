# 🔐 Lab 7 – End-to-End Cloud Security Scenario

> **Category:** Cloud Security  
> **Platform:** AWS  
> **Focus:** Identity • Network • Host • Logs  
> **Level:** Advanced (End-to-End Scenario)  
> **Lab Series:** Cloud Security Labs  

This lab demonstrates a complete **end-to-end cloud security scenario** by combining
identity management, network restrictions, host-level hardening, and log-based verification
into a single, realistic security workflow.

Unlike individual feature-focused labs, this scenario validates how multiple security layers
work together to protect a cloud-hosted Linux server.

---

## 🎯 Objectives

- Enforce **least-privilege identity access** using AWS IAM
- Restrict network exposure using **Security Groups**
- Secure SSH access using **key-based authentication**
- Harden the SSH service at host level
- Validate security controls using **system logs**
- Demonstrate a real-world defense-in-depth approach

---

## 🧪 Environment

| Component | Details |
|---------|--------|
| Cloud Provider | AWS |
| Compute Service | EC2 |
| OS | Ubuntu Server 24.04 LTS |
| Instance Type | t3.micro |
| Region | eu-north-1 |
| Access Method | SSH (Key-based) |
| Log Source | systemd-journald |
| Security Tools | SSH, IAM, Security Groups |

---

## 🧠 Scenario Description

A Linux server is deployed in AWS to simulate a real production-like environment.
Access to this server must be:

- Controlled by **identity (IAM)**
- Restricted at **network level**
- Hardened at **host level**
- Continuously verifiable through **logs**

No default or overly permissive settings are allowed.

---

## 🧱 Step 1 – Identity & Access Model

- Root AWS account usage avoided
- Access managed through IAM (least privilege)
- SSH access allowed only via key-based authentication
- No password-based login permitted

This ensures identity separation and prevents credential-based abuse.

---

## 🌐 Step 2 – Network Security (Security Groups)

A custom Security Group was created with a **default-deny** approach.

### Inbound rules:
- SSH (TCP 22)
- Source: **My IP only**

### Outcome:
- Public SSH exposure eliminated
- Port scanning and brute-force attempts blocked at network layer

---

## 🖥️ Step 3 – Secure EC2 Deployment

An EC2 instance was launched with:
- Ubuntu Server 24.04 LTS
- Key pair authentication
- Restricted Security Group

### SSH Access Test:

```bash
ssh -i ~/Downloads/lab5-key.pem ubuntu@<PUBLIC_IP>
```

- Password prompt not displayed
- Access denied from unauthorized IPs

---

## 🔒 Step 4 – Host-Level SSH Hardening
SSH service configuration was hardened:

```conf
PermitRootLogin no
PasswordAuthentication no
MaxAuthTries 3
```

SSH service restarted to apply changes:
```bash
sudo systemctl restart ssh
```

### Verification:
```bash
sudo sshd -T | egrep "permitrootlogin|passwordauthentication|maxauthtries"
```

---

## 📜 Step 5 – Log-Based Verification
SSH authentication events were inspected using system logs:
```bash
sudo journalctl -u ssh --since "15 minutes ago"
```

Filtered for relevant signals:
```bash
sudo journalctl -u ssh | grep -E "Accepted|Failed|Invalid|preauth|publickey"
```

---

## Observed Behavior:

- Accepted publickey entries for valid access

- No password-based authentication success

- Pre-authentication termination for unauthorized attempts

---

## ✅ Results

- Identity access restricted using IAM principles

- Network exposure minimized via Security Groups

- SSH access secured using key-based authentication

- Host hardened against brute-force attempts

- Security controls validated through logs

 ---

 ## 🧠 Key Takeaways

- Cloud security requires layered controls
- Least privilege must be enforced across identity, network, and host layers
- Logs are essential for validating security posture
- End-to-end security scenarios provide stronger proof than isolated configurations

---

## 🏁 Conclusion

This lab demonstrates a realistic, production-aligned cloud security workflow.
By combining IAM, network restrictions, host hardening, and log validation,
it showcases a complete defense-in-depth strategy suitable for
cloud security, DevOps security, and infrastructure security roles.
