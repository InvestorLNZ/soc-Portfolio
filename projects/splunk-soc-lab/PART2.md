## Part 2 — Adversary Simulation & Detection

Attacks launched from Kali Linux against the domain controller, with each technique mapped to the SIEM response. This was run as a **control-validation exercise**: the goal was not only to detect attacks, but to confirm which hardening controls held. Some attacks were caught by detections; others were blocked outright by domain configuration — and the logs prove both.

> **Scope note:** this validates specific controls against specific techniques tested. It does not claim the domain is "secure" in general — it demonstrates that the configured controls defeated the techniques below.

### Attack chain

All attacks run from Kali (`192.168.0.18`) against the DC (`192.168.0.15`), using a single foothold credential to simulate a post-phishing attacker.

| # | Attack | MITRE ATT&CK | Outcome | Evidence |
|---|---|---|---|---|
| 1 | SMB / service recon (nmap, NetExec) | T1046 | Mapped | AD services enumerated; SMB signing required |
| 2 | RID brute enumeration | T1087 | **Blocked** | SAMR/LSAD returned ACCESS_DENIED |
| 3 | LDAP user enumeration | T1087 | Succeeded | 50 domain users pulled |
| 4 | Password spray (12 accounts) | T1110.003 | **Detected** | Alert fired — 14 accounts in one 10-min window |
| 5 | Kerberoasting (svc-sql01) | T1558.003 | **Blocked** | 0 RC4 tickets issued; AES-only policy enforced |
| 6 | Machine-account-quota check | T1078 | Finding | MAQ = 10 (default; should be 0) |

### Detections built

Two detection rules authored in Splunk SPL.

**1. Password spray — fired.**
A spray is one failed logon across *many* accounts, so per-account thresholds miss it. This rule counts distinct accounts failing in a rolling window:

```spl
index=wineventlog EventCode=4625 earliest=-15m
| bin _time span=10m
| stats dc(Account_Name) as accounts_targeted, values(Account_Name) as accounts by _time
| where accounts_targeted >= 5
```

Result: the 1 Nov spray surfaced as a single window with **14 accounts targeted**, and the scheduled alert triggered automatically. Tuned to a rolling 15-minute window to avoid re-alerting on historical events.

**2. Kerberoasting (RC4) — armed.**
Kerberoasting prefers RC4 (`0x17`) tickets because they crack faster. In an AES-only domain, a single RC4 ticket request is anomalous by definition:

```spl
index=wineventlog EventCode=4769 Ticket_Encryption_Type=0x17 earliest=-15m
| stats count by Account_Name, Service_Name, Ticket_Encryption_Type
```

Over a full week this rule had **zero hits** — because every one of 31 ticket requests used AES256 (`0x12`). The detection is sound and would fire on any RC4 request; the absence of hits is itself evidence the control is working.

### Key finding — controls that held

- **RC4 Kerberoasting defeated by crypto policy.** Even with RC4 enabled on the target service account, the KDC refused to issue an RC4 ticket (`KDC_ERR_ETYPE_NOSUPP`) because domain Kerberos policy is AES-only. The roast never produced a crackable hash. Logs: 31/31 ticket requests at AES256, 0 at RC4.
- **RID enumeration blocked.** Null SMB sessions were permitted, but anonymous RID-cycling over SAMR/LSAD was denied — enumeration via that path failed.
- **SMB signing required.** Confirmed in the nmap scan; blocks SMB relay attacks.

### Findings to remediate

- **MachineAccountQuota = 10** — any authenticated user can add up to 10 computer accounts to the domain, a privilege-escalation building block. Recommend setting to 0.
- **Null SMB sessions allowed** — harden to disallow anonymous access.

### Metrics

| Metric | Value |
|---|---|
| Attack techniques run | 6 |
| Detected by SIEM | 1 (password spray) |
| Blocked by hardening | 2 (RC4 Kerberoasting, RID enumeration) |
| Detection rules authored | 2 (1 firing, 1 armed) |
| ATT&CK techniques covered | T1046, T1087, T1110.003, T1558.003, T1078 |

### Screenshots

Ordered attack → detection.

| File | Shows | Side |
|---|---|---|
| `screenshots/part2/01-nmap-scan.png` | AD service enumeration, SMB signing required | Attacker |
| `screenshots/part2/02-password-spray-kali.png` | Password spray — 12 failed logons | Attacker |
| `screenshots/part2/03-spray-detection-splunk.png` | Spray caught — 14 accounts in one window | SIEM |
| `screenshots/part2/04-spray-alert-triggered.png` | Scheduled detection firing automatically | SIEM |
| `screenshots/part2/05-kerberoast-blocked-kali.png` | Kerberoast blocked — KDC_ERR_ETYPE_NOSUPP | Attacker |
| `screenshots/part2/06-kerberos-encryption-types.png` | 31/31 tickets AES256, 0 RC4 — hardening proof | SIEM |

### What this demonstrates

- Running real adversary techniques against a domain and reading the resulting telemetry.
- Writing detections that match attacker *behaviour* (spray ≠ brute force), not just event IDs.
- Distinguishing a detection that fires from a control that blocks — and documenting both as valid outcomes.
- Scoping security claims to what was actually tested.
