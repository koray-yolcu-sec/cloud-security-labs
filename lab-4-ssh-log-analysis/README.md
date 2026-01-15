# 🔐 Lab 4 – SSH Log Analysis & Intrusion Detection

> **Category:** Cloud & Linux Security  
> **Focus:** SSH Log Analysis, Intrusion Detection  
> **Level:** Intermediate  
> **Lab Series:** Cloud Security Labs  
> **Core Skills:** Log Analysis, Brute-Force Detection, Fail2Ban

This lab focuses on analyzing SSH authentication logs to understand how brute-force attacks are detected and mitigated using system logs and **Fail2Ban**.

Unlike previous labs, the emphasis is on **log visibility**, **event correlation**, and **attack signal interpretation** — core skills required in real-world cloud, infrastructure, and SOC environments.

---

## 🎯 Objectives

* Analyze SSH authentication failures from system logs
* Identify key brute-force attack indicators (invalid user, failed password, preauth)
* Understand how SSH brute-force attempts appear at log level
* Correlate attack attempts with defensive actions
* Validate Fail2Ban detection and automated banning

---

## 🧪 Lab Environment

| Component | Details |
| :--- | :--- |
| **OS** | Kali Linux (UTM VM) |
| **SSH Service** | OpenSSH |
| **SSH Port** | 2222 |
| **Log Source** | systemd-journald |
| **Defense Tool** | Fail2Ban |
| **Attack Type** | SSH Brute-Force |

---

## 🔍 Step 1 – Log Source Discovery

On Kali Linux, the traditional `/var/log/auth.log` file is not used by default. SSH authentication events are recorded via `systemd-journald` instead.

```bash
ls -l /var/log
```
This confirms that no auth.log file is present on the system.

---

## 🔍 Step 2 – Inspect SSH Authentication Failures

SSH-related authentication logs are queried directly from the journal.

```bash
sudo journalctl -u ssh | grep "Failed"
```

### Observed log patterns include:

```text
Failed password for invalid user
error: maximum authentication attempts exceeded
Disconnected: Too many authentication failures
ssh2 [preauth]
```

---

## 🚨 Step 3 – Identify Brute-Force Indicators

Key brute-force indicators observed in logs:

Repeated login attempts from the same IP

Multiple invalid usernames

Rapid successive authentication failures

`preauth` disconnections before session establishment

### Example log entry:

```bash
Failed password for invalid user fakeuser from 127.0.0.1 port 58054 ssh2
```

---

## 🛡️ Step 4 – Validate Fail2Ban Detection

The output confirms that `SSH` failures were detected and an IP address was automatically banned, 
demonstrating effective correlation between log events and automated defensive actions.

```bash
sudo fail2ban-client status sshd
```

---

## 📊 Results & Observations

- ✅ SSH brute-force attempts successfully detected
- ✅ Attack indicators clearly visible at log level
- ✅ Fail2Ban correctly correlated log events
- ✅ Automated IP banning functioned as expected

---

## ✅ Conclusion

This lab demonstrates how SSH brute-force attacks can be detected and analyzed purely through log inspection, 
without relying on external monitoring tools.

Understanding log-level attack signals and validating automated defensive responses are essential skills for 
cloud security, infrastructure security, and SOC-focused roles, and form the foundation of effective incident detection and response.
