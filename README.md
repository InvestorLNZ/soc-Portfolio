# SOC Portfolio — Shemmy Afolayan

BSc Cyber Security (First Class Honours) — York St John University
📍 Gravesend, Kent | 🔗 [LinkedIn](https://linkedin.com/in/shemmy-afolayan) | 🐙 [GitHub](https://github.com/InvestorLNZ)

---

## About

This repository documents my hands-on cybersecurity projects, built to develop and evidence practical skills in defensive security engineering, SIEM deployment, Active Directory hardening, and IoT security.

Each project reflects real-world security patterns and is documented end-to-end — from design decisions through to implementation, testing, and lessons learned.

---

## Projects

### 1. Active Directory Tiered Security Lab with Wazuh SIEM
**Status:** ✅ Complete — SIEM Live  
**Focus:** Enterprise AD hardening, GPO security baselines, SIEM deployment, MITRE ATT&CK threat detection

A tiered Active Directory environment built on Windows Server 2022, implementing a T0/T1/T2 privilege model with GPO security hardening. Wazuh SIEM deployed on a dedicated Ubuntu 24.04 machine, with the Windows Server DC connected as a monitored agent. MITRE ATT&CK tactics actively detected in real time.

**Key technologies:** Windows Server 2022, Active Directory, Group Policy, PowerShell, Wazuh 4.14.4, Ubuntu 24.04

**Highlights:**
- Tiered OU structure (T0/T1/T2) with GPOs linked and verified applying
- Wazuh all-in-one deployment on dedicated hardware
- Windows Server DC agent registered and active
- MITRE ATT&CK detections: Defense Evasion, Privilege Escalation, Initial Access
- All troubleshooting documented end-to-end

[View Project →](projects/active-directory-siem-lab/README.md)

---

### 2. IoT Smart Home Intrusion Detection System
**Status:** ✅ Complete  
**Focus:** IoT security, MQTT, Python automation, secure credential handling, real-time alerting

An end-to-end IoT security alerting system built with Arduino UNO R4 WiFi and Raspberry Pi. Detects motion via PIR sensor and delivers real-time Telegram notifications over a secure MQTT pipeline. Built with security-first design including credential separation and encrypted alert delivery.

**Key technologies:** Arduino, Raspberry Pi, Python, MQTT, Telegram Bot API, ThingSpeak

[View Project →](projects/iot-intrusion-detection/README.md)

---

### 3. AI-Assisted SOC Lab with Splunk
**Status:** 🚧 In Progress — Part 1 of 5 complete (logging pipeline live)
**Focus:** Splunk SIEM deployment, Sysmon detection engineering, log pipeline validation, adversary simulation, AI-assisted triage

A Splunk counterpart to the Wazuh lab above: a Windows Server 2022 domain feeding Sysmon and Windows Security logs into Splunk Enterprise for real-time detection. One domain controller already produces ~72,000 events across 49 event types. Built end-to-end, including the silent failures — clock drift, a malformed forwarder config, and a log backlog — documented as a debugging log. Next phases attack the domain from Kali, build SPL detections, and add an AI triage layer (SENTINEL).

**Key technologies:** Windows Server 2022, Active Directory, Sysmon, Splunk Enterprise, Splunk Universal Forwarder, Ubuntu, Kali Linux

**Highlights:**
- End-to-end log pipeline validated from endpoint to SIEM
- 62-account tiered AD model (T0/T1/T2) across 6 departments
- Sysmon with SwiftOnSecurity config for high-fidelity telemetry
- Full debugging log — the silent failures and how I found them
- Roadmap to SPL detections and AI-assisted triage with MITRE ATT&CK mapping

[View Project →](projects/splunk-soc-lab/README.md)

---

## Skills Evidenced

| Area | Evidence |
|---|---|
| SIEM Deployment | Wazuh and Splunk on Ubuntu, Windows agent / forwarder integration |
| SIEM Engineering (Splunk) | Universal Forwarder, index design, SPL search, Sysmon ingestion |
| Threat Detection | MITRE ATT&CK tactic identification, real-time event monitoring |
| Active Directory | Tiered OU structure, GPO hardening, PowerShell automation |
| Network Security | Home lab segmentation, agent-to-manager communication |
| IoT Security | MQTT pipeline, credential management, encrypted alerting |
| Python | MQTT subscriber, Telegram API integration, automation scripts |
| Linux | Ubuntu server administration, service management, port analysis |
| Documentation | End-to-end technical writeups with architecture diagrams |

---

## Certifications

- Microsoft AZ-900 ✅
- CompTIA Security+ — In Progress
- CompTIA CySA+ — In Progress

---

## Open To Work

Actively seeking **Security Engineer** or **SOC Analyst** roles across the UK and Europe. Open to visa sponsorship. Available for immediate start.
