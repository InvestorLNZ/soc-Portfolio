# Remediation Report — Developer & Operations Guide

**Target System:** Web Application + Linux Server
**Reference:** Cap Security Assessment — June 2026
**Intended Audience:** Developers, System Administrators, DevOps Engineers
**Classification:** CONFIDENTIAL — INTERNAL

---

> This document provides specific, actionable remediation steps for each finding. Each fix includes implementation instructions, verification steps, and estimated effort. Fixes are ordered by priority.

---

## FIX-01 — Remediate IDOR on `/data/{id}` Endpoint

**Finding Reference:** FINDING-01
**Priority:** 🔴 Critical — implement immediately
**Estimated Effort:** 1–2 hours development + testing

### The Problem

The `/data/{id}` route retrieves packet capture records using a user-supplied integer ID without verifying that the requesting user owns that record. Any authenticated user can access any capture by guessing or incrementing the ID.

### The Fix

Add a server-side ownership check before returning any data. The fix must happen on the **server** — client-side checks can be bypassed.

**Python/Flask example (before — vulnerable):**
```python
@app.route('/data/<int:capture_id>')
@login_required
def data(capture_id):
    capture = Capture.query.get(capture_id)
    return render_template('data.html', capture=capture)
```

**Python/Flask example (after — secure):**
```python
@app.route('/data/<int:capture_id>')
@login_required
def data(capture_id):
    capture = Capture.query.filter_by(
        id=capture_id,
        user_id=current_user.id  # Enforce ownership
    ).first_or_404()
    return render_template('data.html', capture=capture)
```

**Django example (after — secure):**
```python
def data(request, capture_id):
    capture = get_object_or_404(
        Capture, id=capture_id, user=request.user
    )
    return render(request, 'data.html', {'capture': capture})
```

### Additional Hardening

Use UUIDs instead of sequential integers for resource IDs — this makes enumeration harder even if an authorisation check is missed:
```python
import uuid
# Store captures with: id = str(uuid.uuid4())
# e.g. /data/550e8400-e29b-41d4-a716-446655440000
```

### Verification

After implementing the fix, test it:
```bash
# Login as user A, note a capture ID
# Logout, login as user B
# Attempt to access user A's capture ID directly in browser
# Expected result: 403 Forbidden or 404 Not Found
```

---

## FIX-02 — Disable FTP, Implement SFTP

**Finding Reference:** FINDING-02
**Priority:** 🔴 Critical — implement immediately
**Estimated Effort:** 30–60 minutes

### The Problem

FTP transmits usernames, passwords, and file content in cleartext over the network. Any attacker with the ability to capture network traffic (or access to a packet capture log) can read credentials directly. There is no valid use case for unencrypted FTP in a modern environment.

### Step 1 — Stop and Disable vsftpd

```bash
sudo systemctl stop vsftpd
sudo systemctl disable vsftpd
```

Verify it's stopped:
```bash
sudo systemctl status vsftpd
# Should show: inactive (dead)
```

### Step 2 — Block FTP Port at Firewall

```bash
# UFW (Ubuntu)
sudo ufw deny 21/tcp
sudo ufw reload

# iptables
sudo iptables -A INPUT -p tcp --dport 21 -j DROP
sudo iptables -A OUTPUT -p tcp --sport 21 -j DROP
```

### Step 3 — Enable SFTP (if file transfers are needed)

SFTP runs over SSH (port 22) — no additional software required if OpenSSH is installed.

Configure SFTP-only access for specific users by adding to `/etc/ssh/sshd_config`:
```
# Restrict a user to SFTP only (no shell access)
Match User ftpuser
    ForceCommand internal-sftp
    PasswordAuthentication yes
    ChrootDirectory /home/ftpuser
    PermitTunnel no
    AllowAgentForwarding no
    AllowTcpForwarding no
    X11Forwarding no
```

Restart SSH:
```bash
sudo systemctl restart sshd
```

### Step 4 — Rotate Compromised Credentials

Since credentials were transmitted in plaintext, all FTP user passwords must be considered compromised and rotated immediately:
```bash
sudo passwd nathan
# Set a new strong password
```

Also audit whether `nathan` uses the same password anywhere else (email, VPN, other services).

### Verification

```bash
# Confirm FTP is no longer accessible
nmap -p 21 localhost
# Expected: 21/tcp filtered

# Confirm SFTP works
sftp nathan@localhost
```

---

## FIX-03 — Remove cap_setuid from Python Binary

**Finding Reference:** FINDING-03
**Priority:** 🔴 Critical — implement immediately
**Estimated Effort:** 15 minutes

### The Problem

The `cap_setuid` capability on `/usr/bin/python3.8` allows any user who can run Python to change their process UID to 0 (root). This effectively grants root access to any user on the system — bypassing sudo, file permissions, and all other access controls.

### Step 1 — Remove the Capability

```bash
sudo setcap -r /usr/bin/python3.8
```

### Step 2 — Verify It's Removed

```bash
getcap /usr/bin/python3.8
# Expected: no output (capability has been removed)
```

Full audit of all capabilities on the system:
```bash
getcap -r / 2>/dev/null
```

Review every result. The only capabilities that should be present are those explicitly required by known system tools (e.g. `ping` requires `cap_net_raw`).

### Step 3 — Audit for Other Privilege Escalation Vectors

While remediating, check for other common escalation paths:

**SUID binaries:**
```bash
find / -perm -4000 -type f 2>/dev/null
```

**World-writable files owned by root:**
```bash
find / -writable -user root -type f 2>/dev/null | grep -v proc
```

**Sudo rights:**
```bash
sudo -l
```

### Why This Capability Was Dangerous

Linux capabilities are a fine-grained alternative to running processes as root. `cap_setuid` specifically allows a process to call `setuid()` to become any user — including root (UID 0). Unlike SUID binaries, capabilities are often overlooked in security audits because they're not visible with a standard `ls -la`.

---

## Post-Remediation Checklist

```
[ ] IDOR fix deployed and tested with two separate user accounts
[ ] FTP service stopped and disabled
[ ] Port 21 blocked at firewall level
[ ] SFTP configured if file transfer capability is required
[ ] Compromised passwords rotated for all affected accounts
[ ] cap_setuid removed from python3.8
[ ] Full capabilities audit completed (getcap -r /)
[ ] SUID binary audit completed
[ ] Penetration tester notified for re-test verification
```

---

## Re-Test

After all fixes are implemented, a re-test should be conducted to verify:
1. `/data/0` returns 403/404 when accessed by a different user
2. Port 21 is closed/filtered
3. `python3.8 -c 'import os; os.setuid(0)'` raises `PermissionError`

---

*Questions about implementation? Escalate to the security team before making changes to production systems.*
