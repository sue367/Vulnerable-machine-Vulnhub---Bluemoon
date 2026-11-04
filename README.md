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

## 1: Reconnaissance & Port Scanning
### 1. Identify Local IP Address
Determine the current network subnet configuration using `ifconfig`[cite: 1]:
```bash
ifconfig eth0

---

### Network Discovery Scan
nmap -sn 192.168.56.0/24

