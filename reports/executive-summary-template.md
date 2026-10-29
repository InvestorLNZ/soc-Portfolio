# Executive Summary Template

**Organisation:** [Client Name]
**Assessment Type:** [Penetration Test / Vulnerability Assessment / Red Team]
**Date:** [Month Year]
**Assessed By:** [Your Name]
**Classification:** CONFIDENTIAL

---

## Overview

[2–3 sentences summarising what was assessed, by whom, and the overall outcome. Keep this jargon-free. Example: "An authorised security assessment was conducted against [Organisation]'s [web application / internal network / cloud infrastructure]. The assessment simulated an attack by [external attacker / insider threat]. [X] vulnerabilities were identified, [X] of which were rated Critical or High severity."]

---

## Risk Summary

| Finding | Risk Level | Business Impact |
|---|---|---|
| [Finding 1 name] | 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low | [One line business impact] |
| [Finding 2 name] | | |
| [Finding 3 name] | | |

**Overall Risk Rating:** [Critical / High / Medium / Low]

[1–2 sentences explaining the overall rating. E.g. "The combination of findings X and Y creates a complete attack path from unauthenticated access to full system compromise."]

---

## What Happened — Plain English

### [Finding 1 — Give it a non-technical name]
[Explain the vulnerability as an analogy. Avoid acronyms. Think: how would you explain this to someone with no IT background? E.g. "The application left a back door unlocked — by changing a single number in a web address, anyone could access files belonging to other users."]

### [Finding 2]
[Same approach — analogy first, then impact.]

### [Finding 3]
[Same approach.]

---

## Business Impact

If exploited by a malicious actor, these findings could result in:

- [Impact 1 — e.g. Unauthorised access to customer data]
- [Impact 2 — e.g. Financial loss through fraud or ransomware]
- [Impact 3 — e.g. Regulatory fines — GDPR, FCA, etc.]
- [Impact 4 — e.g. Reputational damage and loss of customer trust]
- [Impact 5 — e.g. Operational disruption]

---

## Recommendations — Priority Order

| Priority | Action | Estimated Effort |
|---|---|---|
| 🔴 Immediate (within 48 hours) | [Action] | [X days / hours] |
| 🟠 Short-term (within 2 weeks) | [Action] | [X days] |
| 🟡 Medium-term (within 1 month) | [Action] | [X weeks] |
| 🟢 Ongoing | [Recurring action — e.g. quarterly pen testing] | Recurring |

---

## Conclusion

[2–3 sentences. Summarise severity, urgency, and the message you want the executive to take away. Be direct. E.g. "The vulnerabilities identified in this assessment are serious and require immediate attention. They do not require sophisticated techniques to exploit — basic knowledge and freely available tools are sufficient. Remediation should be treated as urgent."]

---

*This report was produced following responsible disclosure principles. For technical details, refer to the accompanying Technical Report.*
