# 🔐 Lab 5 – AWS EC2 Hardening & Secure SSH Access

> **Category:** Cloud Security  
> **Platform:** AWS  
> **Focus:** EC2 Hardening, SSH Security, Least Privilege  
> **Level:** Intermediate

This lab focuses on securely deploying and hardening an AWS EC2 instance by applying
least-privilege network controls and SSH hardening techniques.

---

## 🎯 Objectives

- Launch an EC2 instance with minimal exposure
- Restrict SSH access using Security Groups
- Enforce key-based authentication
- Disable password-based SSH login
- Validate access control through log inspection

---

## 🧪 Environment

- **Cloud Provider:** AWS
- **Service:** EC2
- **OS:** Ubuntu 24.04 LTS
- **Instance Type:** t3.micro
- **Region:** eu-north-1
- **Access Method:** SSH (key-based)

---

## 🔒 Security Hardening Steps

- SSH access restricted to **My IP only**
- `PasswordAuthentication` disabled
- Root login disabled
- Authentication attempts limited
- Changes validated via `journalctl`

---

## 🔍 Verification

- Successful login only with valid SSH key
- Connection attempts from unauthorized sources denied
- SSH events verified through system logs

---

## 🧱 Step 1 – Create EC2 + Key Pair

- Created a key pair and downloaded `lab5-key.pem`
- Launched Ubuntu EC2 instance (t3.micro)

**Local (Mac) key permissions:**
```bash
chmod 400 ~/Downloads/lab5-key.pem
```
### Connect:

```bash
ssh -i ~/Downloads/lab5-key.pem ubuntu@<PUBLIC_IP>
```

## 🛡️ Step 2 – Security Group: Inbound SSH = My IP Only
Configured inbound rule:

Type: SSH

Port: 22

Source: My IP (x.x.x.x/32)

This prevents the instance from being reachable via SSH from unknown IPs.

## 🧪 Step 3 – Validation Tests (Expected Behavior)

### Test A — Wrong path (blocked)

Tried connecting from a different network / IP scenario → expected to fail.

### Test B — Temporary exposure (for testing only)

Temporarily set SSH source to:

`0.0.0.0/0` (open to the internet)

Then observed how SSH behaves at log level and ensured host-side SSH hardening blocks password-based attacks.

- ✅ After testing, inbound was restored back to My IP only.


## 🔒 Step 4 – SSH Hardening (Server-Side)

### Edited SSH config:

```bash
sudo nano /etc/ssh/sshd_config
```

### Applied hardening settings:

```conf
PermitRootLogin no
PasswordAuthentication no
MaxAuthTries 3
```

### Restarted SSH service:

```bash
sudo systemctl restart ssh
```

### Verified the active config:

```bash
sudo sshd -T | egrep "permitrootlogin|passwordauthentication|maxauthtries"
```

## 📜 Step 5 – Log-Based Verification (Proof)

### Checked recent SSH logs:

```bash
sudo journalctl -u ssh --since "20 minutes ago"
```

### Filtered key signals:

```bash
sudo journalctl -u ssh | grep -E "Failed|Invalid|preauth|publickey"
```

---

## Observed signals (evidence)

Accepted publickey for ubuntu ...

Connection closed ... [preauth]

✅ No password-based auth success observed
✅ Unauthorized attempts are terminated at pre-auth stage

---

## ✅ Results

✅ Inbound SSH restricted to My IP (least-privilege network access)

✅ Password authentication disabled (PasswordAuthentication no)

✅ Root login disabled (PermitRootLogin no)

✅ Reduced brute-force surface (MaxAuthTries 3)

✅ Verified via logs (journalctl) with publickey accept + preauth closures

---

## 🧹 Cleanup (Cost Safety)

To avoid unexpected charges:

Terminate the EC2 instance after completing the lab

(Optional) delete unused resources: security group, key pair, EBS snapshots (if created)

---

## ✅ Outcome

This lab demonstrates practical cloud security fundamentals:
secure instance deployment, network-level access control, and
host-level SSH hardening aligned with real-world best practices.
