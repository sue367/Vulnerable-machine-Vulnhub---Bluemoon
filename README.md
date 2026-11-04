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

### Access & Privilege Escalation
# Setting up a Netcat listener on the attacker machine
nc -lvnp 4444

# Checking available sudo privileges
sudo -l

# Spawining a full TTY shell
python3 -c 'import pty; pty.spawn("/bin/bash")'

### Cracking Techniques (Hashes, Files, Keys)
# Hash identification and cracking with John the Ripper
hashid hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt --format=RAW-MD5 hash.txt

# Cracking encrypted zip files
zip2john archive.zip > zip.hash
john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash

# Cracking SSH private key passphrases
python3 /opt/john/ssh2john.py id_rsa > ssh.hash
john --wordlist=/usr/share/wordlists/rockyou.txt ssh.hash
