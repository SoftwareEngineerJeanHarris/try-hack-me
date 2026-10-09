
# Penetration Testing Report
### LabBox-01

---

**Assessment Information**

| Field | Details |
|---|---|
| Target | LabBox-01 |
| IP Address | 10.10.10.45 |
| Assessment Date | 2026-10-08 |
| Tester | Jean Harris |
| Overall Risk | **HIGH** |

---

## 1. Executive Summary

A penetration test was conducted against **LabBox-01** to identify vulnerabilities that could allow unauthorized system access.

### 1.1 Assessment Results

**Overall Risk: HIGH**

The assessment identified two vulnerabilities:

- **F-01:** Publicly Accessible Backup Files
- **F-02:** Misconfigured Sudo Permissions

Together, these vulnerabilities allowed initial access through SSH and subsequent privilege escalation to root.

### 1.2 Business Impact

Successful exploitation could result in:

- Unauthorized system access
- Exposure of sensitive information
- Full administrative control

---

## 2. Scope & Methodology

### 2.1 Assessment Scope

| Component | Details |
|---|---|
| Target IP | `10.10.10.45` |
| Operating System | Linux |
| Assessment Type | Black-box |
| Authorization | Training lab |

### 2.2 Tools Used

- Nmap — Port scanning
- Gobuster — Directory enumeration
- Curl — HTTP requests
- SSH — Remote authentication

### 2.3 Testing Phases

1. Reconnaissance
2. Enumeration
3. Vulnerability Identification
4. Initial Access
5. Privilege Escalation
6. Reporting

---

## 3. Vulnerability Summary

| ID | Finding | Severity | Status |
|---|---|---|---|
| F-01 | Exposed Backup Files | High | Confirmed |
| F-02 | Unsafe Sudo Permissions | High | Confirmed |

---

## 4. Detailed Findings

### F-01: Publicly Accessible Backup Files

**Severity:** HIGH  
**Affected Service:** HTTP (TCP/80)  
**Affected Path:** `/backup/`

#### Description

A publicly accessible backup directory contained an archive with valid system credentials.

#### Technical Evidence

Directory enumeration identified:

~~~text
/backup     (Status: 301)
/admin      (Status: 403)
~~~

The backup archive contained:

~~~text
Username: operator
Password: [REDACTED]
~~~

#### Impact

An unauthenticated attacker could retrieve credentials and potentially gain system access.

#### Remediation

1. Remove sensitive backups from web-accessible directories.
2. Rotate compromised credentials.
3. Restrict access to backup storage.
4. Implement automated exposure scanning.

---

### F-02: Misconfigured Sudo Permissions

**Severity:** HIGH  
**Affected Component:** Linux Sudo Configuration

#### Description

The `operator` account could execute `/usr/bin/find` with root privileges without a password.

#### Technical Evidence

Command:

~~~bash
sudo -l
~~~

Output:

~~~text
(root) NOPASSWD: /usr/bin/find
~~~

This permission was successfully leveraged to execute a privileged shell.

#### Impact

An attacker with access to the affected account could gain complete administrative control.

#### Remediation

1. Remove unnecessary sudo permissions.
2. Implement least-privilege access.
3. Audit `/etc/sudoers` configurations.
4. Restrict privileged command execution.

---

## 5. Conclusion

The assessment demonstrated a successful attack chain resulting in full system compromise.

### Recommended Actions

| Priority | Action |
|---|---|
| High | Remove publicly exposed backups |
| High | Rotate compromised credentials |
| High | Correct sudo permissions |
| Medium | Review system access controls |

---

## Supporting Evidence

- Nmap scan results
- Directory enumeration results
- SSH authentication evidence
- Privilege escalation evidence

---

**Disclaimer:** Hypothetical authorized training environment.
