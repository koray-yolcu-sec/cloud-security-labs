# Lab 1 – SSH Hardening & Secure Access Control

## Objective
Harden an SSH service by eliminating common attack vectors and enforcing secure access controls using industry best practices.

This lab focuses on protecting remote access by disabling insecure defaults and implementing key-based authentication.

---

## Environment
- Operating System: Kali Linux (local VM)
- SSH Server: OpenSSH
- User: Non-root standard user
- Authentication Method: SSH key (ED25519)

---

## Threat Model
The following risks were addressed in this lab:

- Brute-force attacks on SSH password authentication
- Unauthorized root access
- Automated scans targeting default SSH ports
- Credential stuffing and weak passwords

---

### SSH Hardening Configuration

The final SSH daemon configuration applied during this lab is shown below:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
Port 2222
MaxAuthTries 3
```
---

## Lessons Learned
- SSH misconfiguration can lead to complete system compromise
- Key-based authentication is the foundation of secure remote access
- Configuration validation is critical to prevent accidental lockouts
