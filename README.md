# BlueMoon: 2021 — VulnHub Machine Penetration Testing Report

* **Author:** Nurul Suhaila
* **Student ID:** 52215125568
* **Course / Lab:** Lab Week 3 — Vulnerable Machine Penetration Testing & Privilege Escalation
* **Target Machine:** BlueMoon: 2021 (VulnHub)
* **Assessment Date:** 4 November 2026

---

## Executive Summary

This repository documents the complete step-by-step penetration testing walkthrough of the **BlueMoon: 2021** virtual machine from VulnHub. Starting from local network discovery on `192.168.56.0/24`, enumeration uncovered an Apache web server hosting an unlinked directory (`/hidden_text`) containing a QR code with valid FTP credentials. Files downloaded from the FTP server provided a custom password wordlist (`p_lists.txt`) used to brute-force SSH credentials for user `robin`. Privilege escalation was then performed horizontally to `jerry` via command injection in a `sudo`-permitted script (`feedback.sh`), and vertically to `root` by abusing `jerry`'s membership in the `docker` group.

| Step | Phase | Command / Tool Used | Key Finding / Result |
| :--- | :--- | :--- | :--- |
| **Step 1** | Network Setup | `ifconfig` | Attacker IP identified as `192.168.56.101` |
| **Step 2** | Host Discovery | `nmap -sn 192.168.56.0/24` | Target VM identified at `192.168.56.102` |
| **Step 3** | Port Scanning | `nmap -sV -p- 192.168.56.102` | Open ports: `21/tcp` (FTP), `22/tcp` (SSH), `80/tcp` (HTTP) |
| **Step 4** | Web Enumeration | Browser + `gobuster dir` | Found landing page & hidden directory `/hidden_text` |
| **Step 5** | QR Code Decoding | `zxing.org/w/decode` | Extracted FTP credentials (`userftp` / `ftpp@ssword`) |
| **Step 6** | FTP Enumeration | `ftp 192.168.56.102` | Downloaded `information.txt` and `p_lists.txt` |
| **Step 7** | File Inspection | `cat information.txt p_lists.txt` | Found hint for user `robin` & 32-password wordlist |
| **Step 8** | SSH Brute-Force | `hydra -l robin -P p_lists.txt` | Discovered SSH password: `k4rv3ndh4nh4ck3r` |
| **Step 9** | Initial Access | `ssh robin@192.168.56.102` | Logged in as `robin`; captured `user1.txt` |
| **Step 10** | Sudo Enumeration | `sudo -l` | `robin` can run `/home/robin/project/feedback.sh` as `jerry` |
| **Step 11** | Command Injection | `sudo -u jerry .../feedback.sh` | Injected `/bin/bash` into `$feedback` prompt |
| **Step 12** | Horizontal Escalation | `whoami`, `cat /home/jerry/user2.txt` | Switched to `jerry`; captured `user2.txt` |
| **Step 13** | Shell Stabilization | `python -c 'import pty; ...'` | Spawned interactive Bash pseudo-terminal |
| **Step 14** | Group Enumeration | `id`, `docker image ls` | Confirmed `jerry` is in `docker` group (`alpine:latest` present) |
| **Step 15–16** | Root Escalation | `docker run -v /:/mnt ... chroot` | Mounted host `/` and captured `root.txt` |

---

## Step 1: Identify the Local IP Address
First, check the local network interface configuration and active IP address details using ifconfig 
to accurately determine the current subnet environment. 

![Step 1 - Identify the Local IP Address](screenshots/Screenshot%202026-11-25%20235033.png)

## Step 2: Scan the Local Network for Targets 
Next, run an Nmap ping scan (-sn) across the local subnet range to locate the active IP address 
assigned to the Bluemoon virtual machine. 

![Step 2 - Scan the Local Network for Targets](screenshots/Screenshot%202026-11-25%20235043.png)




