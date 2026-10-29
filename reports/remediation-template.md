# Remediation Report Template

**Assessment Reference:** [Assessment Name + Date]
**Intended Audience:** Developers, System Administrators, DevOps Engineers
**Classification:** CONFIDENTIAL — INTERNAL

---

> This document provides specific, actionable remediation steps for each finding. Each fix includes implementation instructions, verification steps, and estimated effort.

---

## FIX-[01] — [Finding Name]

**Finding Reference:** FINDING-[01]
**Priority:** 🔴 Critical / 🟠 High / 🟡 Medium
**Estimated Effort:** [X hours / days]

### The Problem

[Explain what is misconfigured and why it is dangerous. Keep it concise but accurate enough for a developer or sysadmin to understand.]

### The Fix

[Provide specific code, commands, or configuration changes. Always show before and after where possible.]

**Before (vulnerable):**
```[language]
[Vulnerable code or config]
```

**After (secure):**
```[language]
[Fixed code or config]
```

### Verification

[How to confirm the fix worked. Provide a test command or procedure.]
```bash
[Verification command]
# Expected output: [what success looks like]
```

### Additional Hardening

[Optional but valuable — any extra steps that further reduce risk beyond the minimum fix.]

---

*(Repeat FIX block for each finding)*

---

## Post-Remediation Checklist

```
[ ] [Fix 1] implemented and verified
[ ] [Fix 2] implemented and verified
[ ] [Fix 3] implemented and verified
[ ] Affected credentials rotated
[ ] Logs reviewed for evidence of prior exploitation
[ ] Security team notified
[ ] Re-test scheduled with penetration tester
```

---

## Re-Test

After all fixes are implemented, request a re-test to confirm:
1. [Verification test for Fix 1]
2. [Verification test for Fix 2]
3. [Verification test for Fix 3]

---

*Questions about implementation? Escalate to the security team before making changes to production systems.*
