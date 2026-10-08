Penetration Testing Report

Target: LabBox-01
IP Address: 10.10.10.45
Date: October 8, 2026
Tester: [Your Name]
Environment: Hypothetical authorized training lab

1. Executive Summary

A penetration test was conducted against LabBox-01 to identify vulnerabilities that could allow unauthorized access.

Two security vulnerabilities were identified, resulting in successful initial access and privilege escalation to root.

Overall Risk: High

2. Scope & Methodology

Target: 10.10.10.45
Testing Type: Black-box
Tools Used: Nmap, Gobuster, Curl, SSH

Testing consisted of:

Network reconnaissance

Service enumeration

Web directory enumeration

Credential exposure testing

Privilege escalation assessment

3. Findings Summary

ID

Vulnerability

Severity

F-01

Publicly Accessible Backup Containing Credentials

High

F-02

Unsafe Sudo Configuration

High

4. Detailed Findings

F-01: Exposed Backup Files

Severity: High
Affected Service: HTTP (Port 80)

Description:

An accessible /backup/ directory contained an archive exposing valid system user credentials.

Evidence:

Directory discovered through Gobuster.

Backup archive downloaded without authentication.

Credentials extracted from archive.

Credentials successfully used to establish SSH access.

Impact:

An attacker could gain unauthorized access to the operating system using exposed credentials.

Recommendation:

Remove backup files from publicly accessible directories.

Rotate exposed credentials.

Restrict access to sensitive resources.

Implement automated checks for exposed files.

F-02: Misconfigured Sudo Permissions

Severity: High
Affected Component: Linux privilege configuration

Description:

The compromised user account was permitted to execute /usr/bin/find using sudo without authentication.

Evidence:

The sudo -l command revealed:

(root) NOPASSWD: /usr/bin/find

This configuration was successfully leveraged to execute commands with root privileges.

Impact:

An attacker with access to the affected user account could obtain complete administrative control of the system.

Recommendation:

Remove unnecessary sudo permissions.

Follow the principle of least privilege.

Review sudoers configurations.

Restrict execution of utilities capable of launching arbitrary commands.

5. Conclusion

Testing successfully demonstrated a chain of vulnerabilities allowing complete system compromise.

The initial vulnerability exposed SSH credentials, while the second vulnerability allowed escalation from a standard user to root.

Remediation Priority:

Remove exposed backup archives and rotate credentials.

Correct unsafe sudo permissions.

Retesting Status: Not performed.

Disclaimer: Fictional demonstration report created for portfolio and educational purposes.