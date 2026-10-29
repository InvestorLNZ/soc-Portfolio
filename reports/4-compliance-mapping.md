# Compliance & Risk Mapping Report

**Assessment Reference:** Cap Security Assessment — June 2026
**Intended Audience:** Risk Managers, Compliance Officers, Governance Teams, Auditors
**Classification:** CONFIDENTIAL — GOVERNANCE

---

## Purpose

This document maps each identified vulnerability to relevant industry frameworks and compliance standards. It is intended to support risk register updates, audit responses, and regulatory compliance activities.

---

## Frameworks Referenced

| Framework | Relevance |
|---|---|
| NIST Cybersecurity Framework (CSF) 2.0 | Industry standard risk management |
| ISO/IEC 27001:2022 | Information security management |
| OWASP Top 10 (2021) | Web application security |
| CIS Controls v8 | Prioritised security actions |
| UK NCSC Cyber Essentials | UK baseline security standard |

---

## Finding-Level Compliance Mapping

---

### FINDING-01 — Insecure Direct Object Reference (IDOR)

**Risk Rating:** High | **CVSS:** 7.5

#### OWASP Top 10 2021
| Reference | Category |
|---|---|
| A01:2021 | Broken Access Control |

> Broken Access Control is the #1 web application risk in OWASP 2021. IDOR is a direct manifestation — the application fails to verify that the user is authorised to access the requested resource.

#### NIST CSF 2.0
| Function | Category | Subcategory |
|---|---|---|
| PROTECT (PR) | Identity Management & Access Control (PR.AA) | PR.AA-05: Access permissions and authorisations are managed, incorporating principles of least privilege and separation of duties |
| IDENTIFY (ID) | Improvement (ID.IM) | ID.IM-02: Processes and procedures are established and communicated |

#### ISO/IEC 27001:2022
| Control | Description |
|---|---|
| A.8.3 — Information Access Restriction | Access to information and application system functions shall be restricted in accordance with the access control policy |
| A.8.4 — Access to Source Code | Read and write access to source code, development tools and software libraries shall be appropriately managed |

#### CIS Controls v8
| Control | Safeguard |
|---|---|
| CIS Control 6 — Access Control Management | 6.8: Define and maintain role-based access control |

#### UK NCSC Cyber Essentials
| Requirement | Gap |
|---|---|
| Access Control | Users must only be able to access data that they need for their role — IDOR violates this principle |

---

### FINDING-02 — Plaintext FTP Credential Transmission

**Risk Rating:** High | **CVSS:** 7.5

#### OWASP Top 10 2021
| Reference | Category |
|---|---|
| A02:2021 | Cryptographic Failures (formerly Sensitive Data Exposure) |

> The application transmits credentials over an unencrypted channel (FTP). This directly maps to cryptographic failure — sensitive data is exposed due to absence of encryption in transit.

#### NIST CSF 2.0
| Function | Category | Subcategory |
|---|---|---|
| PROTECT (PR) | Data Security (PR.DS) | PR.DS-02: The confidentiality, integrity, and availability of data-in-transit are protected |

#### ISO/IEC 27001:2022
| Control | Description |
|---|---|
| A.8.24 — Use of Cryptography | Rules for the effective use of cryptography, including cryptographic key management, shall be defined and implemented |
| A.8.26 — Application Security Requirements | Information security requirements shall be specified when developing or procuring applications |

#### CIS Controls v8
| Control | Safeguard |
|---|---|
| CIS Control 3 — Data Protection | 3.10: Encrypt sensitive data in transit |

#### UK NCSC Cyber Essentials
| Requirement | Gap |
|---|---|
| Secure Configuration | Network services must be configured to minimise exposure — FTP exposes credentials on the network |

#### Additional Regulatory Consideration — UK GDPR
If any personal data was transmitted via FTP or accessible via the captured network traffic, this finding may constitute a reportable data breach under UK GDPR Article 33. The ICO must be notified within 72 hours of becoming aware of a breach likely to result in risk to individuals' rights and freedoms.

---

### FINDING-03 — Linux Capability Misconfiguration (cap_setuid)

**Risk Rating:** High | **CVSS:** 7.8

#### NIST CSF 2.0
| Function | Category | Subcategory |
|---|---|---|
| PROTECT (PR) | Identity Management & Access Control (PR.AA) | PR.AA-05: Access permissions incorporate principles of least privilege |
| PROTECT (PR) | Platform Security (PR.PS) | PR.PS-01: Configuration management practices are established and applied |
| DETECT (DE) | Adverse Event Analysis (DE.AE) | DE.AE-02: Potentially adverse events are analysed to better characterise them |

#### ISO/IEC 27001:2022
| Control | Description |
|---|---|
| A.8.2 — Privileged Access Rights | The allocation and use of privileged access rights shall be restricted and managed |
| A.8.8 — Management of Technical Vulnerabilities | Information about technical vulnerabilities of information systems in use shall be obtained in a timely fashion |
| A.8.9 — Configuration Management | Configurations shall be established, documented, implemented, monitored, and reviewed |

#### CIS Controls v8
| Control | Safeguard |
|---|---|
| CIS Control 4 — Secure Configuration | 4.1: Establish and maintain a secure configuration process |
| CIS Control 5 — Account Management | 5.4: Restrict administrator privileges to dedicated administrator accounts |

#### UK NCSC Cyber Essentials
| Requirement | Gap |
|---|---|
| Secure Configuration | Systems should be configured to reduce attack surface — unnecessary capabilities must be removed from standard binaries |

---

## Risk Register Summary

The following entries are recommended for addition to the organisational risk register:

| Risk ID | Risk Description | Likelihood | Impact | Risk Score | Owner |
|---|---|---|---|---|---|
| RISK-001 | Unauthorised access to user data via broken access controls | High | High | Critical | Application Owner |
| RISK-002 | Credential theft via unencrypted network protocols | Medium | High | High | Infrastructure Lead |
| RISK-003 | Privilege escalation via Linux capability misconfiguration | Medium | Critical | High | Systems Administrator |

**Scoring Method:** Qualitative 3x3 matrix (Likelihood × Impact)

---

## Compliance Gap Summary

| Standard | Control Gap | Status |
|---|---|---|
| OWASP A01 — Broken Access Control | IDOR on data endpoint | ❌ Non-compliant |
| OWASP A02 — Cryptographic Failures | FTP plaintext transmission | ❌ Non-compliant |
| ISO 27001 A.8.3 — Access Restriction | No ownership check on resources | ❌ Non-compliant |
| ISO 27001 A.8.24 — Cryptography | No encryption on data in transit | ❌ Non-compliant |
| ISO 27001 A.8.2 — Privileged Access | Unnecessary cap_setuid capability | ❌ Non-compliant |
| NIST PR.AA-05 — Least Privilege | Python binary has excessive capabilities | ❌ Non-compliant |
| NIST PR.DS-02 — Data in Transit | FTP credentials exposed on network | ❌ Non-compliant |
| CIS Control 3.10 — Encryption in Transit | FTP used in place of SFTP | ❌ Non-compliant |
| CIS Control 6.8 — RBAC | No role-based access on captures | ❌ Non-compliant |
| Cyber Essentials — Access Control | Users can access other users' data | ❌ Non-compliant |

---

## Recommended Actions for Compliance Team

1. **Update risk register** with RISK-001 through RISK-003 above
2. **Assess UK GDPR exposure** — determine whether any personal data was accessible via the IDOR or FTP capture, and whether ICO notification is required
3. **Schedule re-assessment** — post-remediation penetration test to close compliance gaps
4. **Review scope of Cyber Essentials certification** — current findings indicate the organisation would not pass Cyber Essentials in its current state
5. **Initiate access control audit** — review all web application endpoints for similar authorisation weaknesses

---

*This compliance mapping is provided for informational purposes and does not constitute legal advice. Consult qualified legal counsel regarding regulatory obligations.*
