# Lab 2 – Firewall & Network Hardening

## Objective
Restrict network access to the system by implementing a minimal firewall policy.
Only explicitly required services should be reachable from the network.

This lab focuses on enforcing a default-deny network posture.

---

## Environment
- Operating System: Kali Linux (local VM)
- Firewall Technology: iptables
- SSH Port: 2222

---

## Threat Model
The following risks were addressed in this lab:

- Unauthorized network access
- Exposure of unnecessary services
- Automated port scanning
- Lateral movement opportunities

---

## Firewall Strategy
A default-deny firewall policy was implemented with the following principles:

- Drop all incoming traffic by default
- Allow only established connections
- Permit SSH access on a non-standard port
- Allow loopback traffic for system stability

---

## Firewall Rules Applied

### Reset existing rules
```bash
sudo iptables -F
sudo iptables -X
```
### Default policies
```bash
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT
```

### Allow loopback traffic
```bash
sudo iptables -A INPUT -i lo -j ACCEPT
```
### Allow established connections
```bash
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```
### Allow SSH on port 2222
```bash
sudo iptables -A INPUT -p tcp --dport 2222 -m state --state NEW,ESTABLISHED -j ACCEPT
```
### Verification

### Open ports check
```bash
ss -tuln
```

### Expected result:
Only port 2222 is listening (IPv4 & IPv6)

### Connection tests
```bash
nc -zv localhost 22
```
Result: Connection Refused
```bash
nc -zv localhost 2222
```
Result: open

---

## Security Consideration: Public Exposure (0.0.0.0/0)

Allowing access from `0.0.0.0/0` exposes services to the public internet and significantly increases attack surface.

While this configuration is acceptable for learning and testing environments, production systems should restrict access to:
- Corporate VPN IP ranges
- Bastion hosts
- Trusted CIDR blocks only

This lab uses open access solely for demonstration purposes.

---

### Lessons Learned

A default-allow firewall is a security risk

Minimal exposure significantly reduces attack surface

Firewall rules must align with service hardening

---

## Cloud Perspective: Security Groups vs Network ACLs

In AWS environments, Security Groups and Network ACLs serve different purposes.

Security Groups are stateful and evaluate all rules collectively, making them suitable for instance-level access control.
Network ACLs are stateless and process rules in numerical order, requiring explicit allow and deny rules.

For most application-level access control scenarios, Security Groups provide simpler management and lower operational risk compared to Network ACLs.

