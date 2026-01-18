# Security Groups – Network-Level Access Control Notes

## What a Security Group Really Is

AWS Security Groups are not traditional firewalls.
They are **stateful, allow-only network filters** enforced at the instance level.

There is no concept of explicit deny rules.

---

## Default Deny by Design

If a packet does not match an allow rule:
→ it is dropped implicitly.

This makes Security Groups naturally aligned with
the least-privilege principle.

---

## Stateful Behavior Matters

Because Security Groups are stateful:
- Allowed inbound traffic automatically allows response traffic
- No need to define outbound return rules

This reduces rule complexity but requires careful inbound design.

---

## Common Misconceptions

- “Opening 0.0.0.0/0 is fine temporarily” → increases attack surface immediately
- “Security Groups replace host firewalls” → false
- “SSH hardening alone is enough” → false

Defense must be layered.

---

## Practical Takeaway

Security Groups should:
- Minimize exposed ports
- Restrict source CIDRs aggressively
- Be combined with host-level controls (iptables, SSH hardening)

Network security is the **first gate**, not the only one.
