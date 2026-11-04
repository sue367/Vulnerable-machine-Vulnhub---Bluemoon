# Vulnerable-machine-Vulnhub---Bluemoon
Practical walkthroughs and scripts for ethical hacking labs, focusing on Linux privilege escalation, web enumeration, and credential brute-forcing
# System Hacking Lab - Week 3: Privilege Escalation & Brute Force

This repository contains documentation, commands, and walkthrough steps for the **Week 3 Lab**, focusing on system hacking stages, reconnaissance, vulnerability scanning, gaining access, privilege escalation, and credential cracking using the **BlueMoon: 2021** VulnHub machine.

---

## Lab Objectives
* Identify and exploit basic system vulnerabilities in a controlled, safe environment.
* Perform network discovery, port scanning, and web enumeration.
* Execute privilege escalation simulations (Vertical, Horizontal, and Hybrid).
* Perform credential, hash, and file cracking using tools like John the Ripper and Hashcat.
* Apply defensive strategies for each system hacking stage.

---

## System Hacking Lifecycle Applied

| Stage | Actions & Methodology | Common Tools / Commands |
| :--- | :--- | :--- |
| **1. Reconnaissance** | Gathering target IP, active hosts, and MAC addresses. | `netdiscover`, `nmap -sn`, `ifconfig` |
| **2. Scanning** | Port scanning, service version detection, and web directory brute-forcing. | `nmap -sC -sV -Pn`, `gobuster`, `dirb` |
| **3. Gaining Access** | Exploiting vulnerabilities, file uploads, or web shells to get initial entry. | Netcat (`nc`), Reverse Shells, Metasploit |
| **4. Privilege Escalation** | Moving from a standard low-privilege user to root/admin (checking `sudo -l`, SUID binaries). | `sudo -l`, `su`, SUID search, GTFOBins |
| **5. Maintaining Access** | Establishing persistence via backup scripts, SSH keys, or cron jobs. | Modifying `/etc/sudoers`, adding backdoor scripts |
| **6. Clearing Tracks** | Removing artifacts, truncating logs, and clearing shell history. | `history -c`, clearing `.bash_history` |

---

## Target Information
* **Machine Name:** BlueMoon: 2021
* **Author:** Kirthik [H4ck3r]
* **Goal:** Get Root!
* **Difficulty:** Easy

---

## Key Commands Reference

### Reconnaissance & Scanning
```bash
# Host discovery
nmap -sn 192.168.115.0/24

# Comprehensive port & service scan
nmap -sC -sV -Pn -vv <TARGET_IP>

# Web directory enumeration
dirb http://<TARGET_IP> /path/to/wordlist.txt -X .php,.html,.txt
gobuster dir -u http://<TARGET_IP> --wordlist /usr/share/wordlists/directory-list-2.3-small.txt

