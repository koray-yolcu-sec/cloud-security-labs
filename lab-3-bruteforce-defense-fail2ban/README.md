# 🔐 Lab 3 – SSH Brute-Force Defense with Fail2Ban

> **Category:** Cloud & Linux Security  
> **Focus:** Host-Based Intrusion Prevention  
> **Level:** Beginner → Intermediate  
> **Lab Series:** Cloud Security Labs  

This lab demonstrates how to protect an SSH service against brute-force attacks using **Fail2Ban**.  
By analyzing authentication logs and automatically banning malicious IP addresses, Fail2Ban provides an effective host-level defense mechanism against credential-based attacks.

---

## Lab Overview

| Item | Description |
|-----|------------|
| Attack Type | SSH Brute-Force |
| Defense Mechanism | Fail2Ban |
| Detection Method | Log-based |
| Response Type | Automatic IP Ban |
| Protected Service | OpenSSH |
| SSH Port | 2222 |
| Tested From | External Host (macOS) |

---

## Lab Goals

- Detect repeated failed SSH authentication attempts
- Automatically ban malicious IP addresses
- Validate protection using a real external attack simulation
- Understand Fail2Ban’s role in host-based security
- Compare host-level defenses with cloud-native controls

---

## Environment
- Host OS: macOS
- Virtualization: UTM
- Guest OS: Kali Linux
- Security Tool: Fail2Ban
- Protected Service: OpenSSH
- SSH Port: 2222
- Network Range: 192.168.64.0/24

---

## Threat Model
The following risks were addressed in this lab:

- SSH brute-force attacks
- Credential stuffing attempts
- Automated login abuse
- Unauthorized remote access
- Lateral movement after credential compromise

---

## Fail2Ban Strategy
Fail2Ban was configured to monitor SSH authentication logs and automatically ban IP addresses that exceed a defined number of failed login attempts.

Security principles applied:

- Log-based intrusion detection
- Automated, temporary banning of malicious IPs
- Protection of non-default SSH port
- Minimal configuration with explicit rules

Fail2Ban operates at the host level by dynamically inserting firewall rules
to block malicious IP addresses based on log pattern matching.

---

## Fail2Ban Installation

```bash
sudo apt update
sudo apt install fail2ban -y
```
---

### Enable and start the service:

```bash
sudo systemctl enable --now fail2ban
sudo systemctl status fail2ban --no-pager
```

---

## Fail2Ban Configuration

### Create local configuration file

```bash
cd /etc/fail2ban
sudo cp jail.conf jail.local
```

### Configure jail.local

```bash
sudo nano /etc/fail2ban/jail.local
```


```ini
[DEFAULT]
bantime  = 600
findtime = 600
maxretry = 3

[sshd]
enabled  = true
port     = 2222
logpath  = %(sshd_log)s
```

### Restart Fail2Ban:

```bash
sudo systemctl restart fail2ban
```

---

## Configuration Validation

### Test configuration syntax

```bash
sudo fail2ban-server -t
```

### Expected result:

- OK: configuration test is successful

---

## Jail Verification

This step verifies that the SSH jail is active and monitored by Fail2Ban.

```bash
sudo fail2ban-client status
```

### Expected output:

```text
Number of jail: 1
Jail list: sshd
```

```bash
sudo fail2ban-client status sshd
```

---

## Attack Simulation (External Host)

> Note: Localhost-based tests may not reliably trigger Fail2Ban due to SSH’s internal authentication limits.  
> Therefore, an external host was used to simulate a realistic attack scenario.

- An SSH brute-force attempt was simulated from the macOS host system targeting the Kali Linux VM.

```bash
ssh -p 2222 fakeuser@192.168.64.9
```

- Multiple invalid passwords were intentionally entered.

---

## Detection & Response

- After exceeding the configured retry threshold, Fail2Ban automatically blocked the attacking IP.

```bash
sudo fail2ban-client status sshd
```

### Observed result:

```text
Currently banned: 1
Total banned: 1
Banned IP list: 192.168.64.1
```

- This confirms that Fail2Ban successfully detected and mitigated a real brute-force attack originating from an external host.

---

## Ban Behavior Observed

- SSH connections from the banned IP timed out

- No further authentication prompts were allowed

- Ban applied at firewall level by Fail2Ban

- No manual intervention required

---

## Cleanup (Optional)

```bash
sudo fail2ban-client set sshd unbanip 192.168.64.1
```

---

## Lessons Learned

- SSH brute-force attacks are trivial to automate

- Non-default ports reduce noise but do not stop attacks

- Log-based detection is effective for authentication abuse

- External attack simulation provides stronger validation than localhost tests

- Fail2Ban significantly improves SSH security with minimal overhead

- Host-based intrusion prevention complements, but does not replace, network firewalls

---

## Cloud Security Perspective

Fail2Ban provides host-based intrusion prevention, while cloud-native firewalls operate at the network perimeter.

### In cloud environments:

- Fail2Ban complements Security Groups and Network ACLs
- Useful for protecting bastion hosts and legacy services
- Provides granular, log-aware defense not available at network layer
- Fail2Ban remains a valuable control even in cloud-native architectures.
