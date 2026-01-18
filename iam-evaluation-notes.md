# IAM Policy Evaluation – Security Notes

## Why IAM Policy Evaluation Matters

In AWS, access control is not based on intent but on **explicit evaluation rules**.
If a permission is not explicitly allowed, it is denied by default.

Understanding how IAM evaluates policies is critical to enforcing least privilege.

---

## Policy Evaluation Order (High-Level)

1. Explicit Deny (always wins)
2. Explicit Allow
3. Default Deny (implicit)

This means that even if a permission is allowed in one policy,
a single explicit deny anywhere will block the action.

---

## Key Security Observations

- Least privilege is enforced by **absence of permissions**, not complexity
- Overlapping policies increase the risk of unintended access
- Group-based permissions scale better than user-based permissions

---

## Why Explicit Deny Is Rare but Powerful

Explicit deny should be used carefully:
- To enforce guardrails
- To block high-risk actions (e.g., deleting logs)

Overusing explicit deny increases operational complexity.

---

## Practical Takeaway

AccessDenied errors are not failures.
They are **verification signals** that least privilege is working as intended.
