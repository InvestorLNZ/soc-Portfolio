# Technical Penetration Test Report Template

**Target:** [IP / Domain / Application Name]
**OS:** [Operating System + Version]
**Assessment Date:** [Date]
**Tester:** [Your Name]
**Classification:** CONFIDENTIAL — TECHNICAL

---

## Scope & Methodology

**Scope:** [Describe what was in scope — IPs, domains, applications]
**Methodology:** [Black-box / Grey-box / White-box]
**Framework:** [PTES / OWASP / OSSTMM]
**Phases:** Reconnaissance → Enumeration → Exploitation → Post-Exploitation

---

## 1. Reconnaissance & Enumeration

### 1.1 Port Scan

**Tool:** Nmap [version]
**Command:**
```bash
nmap -sC -sV -oN nmap_initial.txt [target]
```

**Results:**

| Port | State | Service | Version |
|---|---|---|---|
| [port]/tcp | Open | [service] | [version] |

### 1.2 [Service] Enumeration

[Add a subsection per service. Document what you tried, what worked, what didn't.]

### 1.3 Web Application Enumeration

**HTTP Title:** [Page title]
**Server:** [Server header]

**Directory brute force:**
```bash
gobuster dir -u http://[target] \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x php,py,html,txt -o gobuster.txt
```

**Discovered endpoints:**

| Endpoint | Status | Notes |
|---|---|---|
| `/` | [code] | [description] |

---

## 2. Vulnerability Findings

---

### FINDING-[01] — [Vulnerability Name]

**Severity:** [Critical / High / Medium / Low / Informational]
**CVSS v3.1 Score:** [X.X]
**CVSS Vector:** [AV:X/AC:X/PR:X/UI:X/S:X/C:X/I:X/A:X]

**Description:**
[Explain what the vulnerability is technically. What is misconfigured or poorly implemented? What does it allow an attacker to do?]

**Proof of Concept:**
```bash
[Command or request that demonstrates the vulnerability]
```

**Evidence:**
[Describe what you observed. Reference screenshots, outputs, or captured data.]

**Impact:**
[What can an attacker do with this? Be specific about data, access level, and downstream impact.]

---

*(Repeat FINDING block for each vulnerability)*

---

## 3. Attack Chain Summary

```
[Attacker]
    │
    ▼
[Step 1 — e.g. Port scan reveals open services]
    │
    ▼
[Step 2 — e.g. Web enumeration finds vulnerable endpoint]
    │
    ▼
[Step 3 — e.g. Exploit → initial access]
    │
    ▼
[Step 4 — e.g. Post-exploitation → privilege escalation]
    │
    ▼
[OUTCOME — e.g. FULL SYSTEM COMPROMISE / DATA ACCESS]
```

**Time to compromise:** [X minutes/hours]
**Skill level required:** [Low / Medium / High]

---

## 4. Tools Used

| Tool | Purpose |
|---|---|
| Nmap | Port scanning and service enumeration |
| Gobuster | Web directory enumeration |
| [Tool] | [Purpose] |

---

## 5. Remediation Summary

| Finding | Remediation | Priority |
|---|---|---|
| FINDING-01 | [Fix summary] | Critical |
| FINDING-02 | [Fix summary] | High |

Full detail in [Remediation Report](./3-remediation-report.md).

---

*Document any deviations from scope or unexpected findings in an appendix.*
