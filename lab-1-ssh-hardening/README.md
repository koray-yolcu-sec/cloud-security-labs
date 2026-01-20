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

## SSH Hardening Configuration

The following SSH configuration changes were applied to reduce the attack surface:

- Root login was disabled to prevent direct privileged access
- Password authentication was disabled to mitigate brute-force attacks
- SSH port was changed from default to reduce automated scans
- Authentication attempts were limited

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
Port 2222
MaxAuthTries 3
```
---

## Key-Based Authentication Setup
Generate SSH key
```bash
- ssh-keygen -t ed25519 -C "lab-1-ssh-key"
```
Configure authorized keys
```bash
- mkdir -p ~/.ssh
- cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
- chmod 700 ~/.ssh
- chmod 600 ~/.ssh/authorized_keys
```
Restart SSH
```bash
- sudo systemctl restart ssh
```
---

## Verification

The active SSH configuration was verified using the following command:
```bash
- sudo sshd -T | egrep 'permitrootlogin|passwordauthentication|pubkeyauthentication|port|maxauthtries'
```
A passwordless SSH login was successfully tested:
```bash
- ssh -p 2222 cloudsec@localhost
```
---

## Lessons Learned
- SSH misconfiguration can lead to complete system compromise
- Key-based authentication is the foundation of secure remote access
- Configuration validation is critical to prevent accidental lockouts
