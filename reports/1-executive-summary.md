# Executive Summary — Security Assessment Report

**Organisation (Simulated):** CapCorp Internal Systems
**Assessment Type:** Penetration Test
**Date:** June 2026
**Assessed By:** Shemmy Afolayan, BSc Cyber Security
**Classification:** CONFIDENTIAL

---

## Overview

An authorised security assessment was conducted against an internal-facing web application and its supporting infrastructure. The assessment simulated an attack by an external malicious actor who had gained access to the internal network.

**The outcome was critical: full administrative control of the target system was achieved within a single assessment session.**

Three security weaknesses were identified and exploited in sequence, ultimately granting the attacker unrestricted access to the entire server — including all data, configurations, and connected systems.

---

## Risk Summary

| Finding | Risk Level | Business Impact |
|---|---|---|
| Unauthorised access to sensitive network recordings | 🔴 High | Credential and data exposure |
| Passwords transmitted without encryption | 🔴 High | Account takeover |
| System misconfiguration allows privilege escalation | 🔴 High | Full server compromise |

**Overall Risk Rating: CRITICAL**
All three vulnerabilities are chained together. An attacker exploiting them in sequence achieves complete control of the system with minimal technical skill required.

---

## What Happened — Plain English

### Step 1: We found a security camera with no lock on it
The web application provides a security monitoring dashboard. We discovered that by changing a single number in a web address, we could view another user's recorded network activity — without any password or permission check. This is the equivalent of a building's CCTV footage being accessible to anyone who knows the room number.

### Step 2: The footage contained sensitive information
Inside the recorded network traffic, we found a username and password being transmitted in plain, readable text — like a postcard rather than a sealed letter. We were able to read these credentials directly using standard network analysis tools.

### Step 3: Those credentials unlocked the building — and then the master key room
Using the recovered password, we logged into the server as a legitimate user. From there, we discovered a system misconfiguration that allowed us to promote our own access level to full administrator (root). At this point, every file, user account, configuration, and connected service on the system was under our control.

---

## Business Impact

If this system were a real production environment, the consequences could include:

- **Data breach** — full access to all stored files and databases
- **Credential theft** — all user accounts on the system compromised
- **Lateral movement** — using this server as a foothold to attack other systems on the network
- **Regulatory exposure** — potential GDPR violations if personal data was accessed
- **Reputational damage** — if the breach were disclosed publicly

---

## Recommendations — Priority Order

| Priority | Action | Estimated Effort |
|---|---|---|
| 🔴 Immediate | Implement authorisation checks on all data endpoints | 1–2 days development |
| 🔴 Immediate | Replace FTP with SFTP or SCP for all file transfers | Half day configuration |
| 🔴 Immediate | Audit and remove unnecessary Linux capability assignments | 1 hour system administration |
| 🟡 Short-term | Conduct a full audit of web application access controls | 1–2 weeks |
| 🟡 Short-term | Implement network monitoring to detect credential theft | 2–3 days |
| 🟢 Ongoing | Schedule quarterly penetration testing | Recurring |

---

## Conclusion

The vulnerabilities identified in this assessment represent fundamental security gaps rather than sophisticated attack vectors. They required no specialist tools or advanced techniques to exploit — only basic knowledge and widely available software.

**Remediation of all three findings should be treated as urgent.** The combination of these weaknesses creates a complete attack path from unauthenticated web visitor to full system administrator, and the individual components are each independently dangerous.

---

*This report was produced following responsible disclosure principles. All testing was performed in an authorised environment.*
