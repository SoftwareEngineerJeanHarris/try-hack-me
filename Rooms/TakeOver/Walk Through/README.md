
# LabBox-01 — Walkthrough

**Platform:** TryHackMe
**Difficulty:** Easy  
**OS:** Linux  
**Status:** Root Compromised


## Overview

This walkthrough documents the methodology used to scan, enumerate, exploit, and a hidden flag in TakeOver.

### Skills Practiced

- Network reconnaissance
- Service enumeration
- Web directory discovery
- Web subdomain discovery


## 0. Reconnaissance

### 0.1 Initial Settup

**Command:**

~~~bash
sudo nano /etc/hosts
~~~

**Modifications:**

~~~text
10.67.136.60 futurevera.thm
10.67.136.60 *.futurevera.thm
~~~

## 1. Reconnaissance

### 1.1 Initial Port Scan

**Command:**

~~~bash
sudo nmap -Pn -sV 10.67.136.60
~~~

**Results:**

~~~text
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http     Apache httpd 2.4.41 ((Ubuntu))
443/tcp open  ssl/http Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
~~~

### 1.2 Analysis

2 services were identified:

| Port | Service | Next Action |
|---|---|---|
| 22 | SSH | Identify credentials |
| 80 | HTTP | Enumerate directories |
| 80 | HTTPS | Enumerate directories |


## 2. Enumeration

### 2.1 Directory Enumeration

**Command:**

~~~bash
gobuster dir -u https://futurevera.thm -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -k
~~~

**Results:**

~~~text
assets               (Status: 301) [Size: 319] [--> https://futurevera.thm/assets/]
css                  (Status: 301) [Size: 316] [--> https://futurevera.thm/css/]
js                   (Status: 301) [Size: 315] [--> https://futurevera.thm/js/]
~~~

### 2.2 Subdomain Enumeration

**Command:**

~~~bash
gobuster vhost -u https://futurevera.thm -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -k
~~~

**Results:**

~~~text
blog.futurevera.thm Status: 421 [Size: 408]
support.futurevera.thm Status: 421 [Size: 411]
~~~

### 2.3 Interesting Findings

The `support.futurevera.thm` subdomain had a bad certificate revealing "DNS Name: secrethelpdesk934752.support.futurevera.thm".

**etc/hosts:**

~~~bash
# THM > TakeOver
10.67.136.60 futurevera.thm
10.67.136.60 *.futurevera.thm
10.67.136.60 support.futurevera.thm
10.67.136.60 secrethelpdesk934752.support.futurevera.thm
~~~

## 3. Attack Chain

~~~text
[ Nmap Port Scan ]
        |
        v
[ HTTP Enumeration ]
        |
        v
[ Exposed Subdomains ]
        |
        v
[ HTTP Enumeration ]
~~~

---

**Disclaimer:** Educational demonstration in a hypothetical authorized lab.
