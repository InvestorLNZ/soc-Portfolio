# Penetration Testing Report Writing Guide

**Author:** Shemmy Afolayan | BSc Cyber Security (First Class) | [GitHub: InvestorLNZ](https://github.com/InvestorLNZ)

---

## What This Project Is

One of the most underrated skills in cybersecurity is the ability to communicate findings clearly — not just to other technical people, but to executives, developers, compliance teams, and risk managers. A technically perfect pentest is worthless if the report doesn't land with its audience.

This project demonstrates how to write the same penetration test findings for four different audiences, using a real HackTheBox machine (Cap) as the worked example.

---

## Why Reporting Matters

| Audience | What They Care About |
|---|---|
| C-Suite / Executive | Business risk, cost, reputation |
| Security / IT Team | Technical details, attack chain, tools |
| Dev / Ops Team | How to fix it, specific commands |
| Compliance / Risk | Framework alignment, audit trail |

The same vulnerability means something different to each audience. A good security professional knows how to translate findings accordingly.

---

## Target Machine: HackTheBox — Cap (Easy)

**IP:** 10.129.13.171
**OS:** Ubuntu Linux
**Services:** FTP (21), SSH (22), HTTP (80)

### Vulnerabilities Found

| # | Vulnerability | Severity | CVSS |
|---|---|---|---|
| 1 | Insecure Direct Object Reference (IDOR) | High | 7.5 |
| 2 | Plaintext FTP Credential Transmission | High | 7.5 |
| 3 | Linux Capability Misconfiguration (cap_setuid) | High | 7.8 |

### Attack Summary

```
Nmap scan → HTTP Security Dashboard (port 80)
→ IDOR on /data/0 → downloaded PCAP file
→ Wireshark analysis → plaintext FTP credentials (nathan:Buck3tH4TF0RM3!)
→ SSH login as nathan
→ getcap reveals python3.8 has cap_setuid
→ python3.8 setuid(0) → root shell
→ Full system compromise
```

---

## Report Documents

### Cap HTB — Worked Example
- [1. Executive Summary](./cap-htb/1-executive-summary.md) — For C-Suite and non-technical stakeholders
- [2. Technical Report](./cap-htb/2-technical-report.md) — For security engineers and IT teams
- [3. Remediation Report](./cap-htb/3-remediation-report.md) — For developers and operations teams
- [4. Compliance Mapping](./cap-htb/4-compliance-mapping.md) — For risk and governance teams

### Reusable Templates
- [Executive Summary Template](./templates/executive-summary-template.md)
- [Technical Report Template](./templates/technical-report-template.md)
- [Remediation Report Template](./templates/remediation-template.md)

---

## Key Lessons From This Assessment

1. **IDOR is simple but devastating** — a sequential ID in a URL gave full access to another user's captured network traffic
2. **FTP is a legacy risk** — transmitting credentials in plaintext in 2024 is negligent; SFTP or SCP should be standard
3. **Linux capabilities are a blind spot** — most organisations check SUID binaries but miss capability misconfigurations

---

## Skills Demonstrated in This Project

- Penetration testing methodology (enumeration → exploitation → post-exploitation)
- Network traffic analysis with Wireshark
- Linux privilege escalation research
- Risk communication across technical and non-technical audiences
- Report writing aligned to industry standards (NIST, ISO 27001, OWASP)

---

*This project uses a retired HackTheBox machine for educational purposes. All testing was performed in an authorised lab environment.*
