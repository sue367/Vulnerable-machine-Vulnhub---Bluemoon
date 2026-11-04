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

### Step 1: Identify the Local IP Address
Check the local network interface configuration and active IP address details using `ifconfig` to accurately determine the current subnet environment[cite: 1].
* **Local IP Address:** `192.168.56.101`[cite: 1]

### Step 2: Scan the Local Network for Targets
Run an Nmap ping scan (`-sn`) across the local subnet range (`192.168.56.0/24`) to locate active IP addresses assigned to virtual machines on the network[cite: 1, 2].
* Discovered target IP address: `192.168.56.102`[cite: 2]

### Step 3: Scan Open Ports and Services
Run a comprehensive full TCP port scan (`-sV -p-`) against the target to identify active listening network services and exact version details[cite: 2, 3]:
* **Port 21/tcp:** FTP (`vsftpd 3.0.3`)[cite: 2, 3]
* **Port 22/tcp:** SSH (`OpenSSH 7.9p1 Debian`)[cite: 2, 3]
* **Port 80/tcp:** HTTP (`Apache httpd 2.4.38`)[cite: 2, 3]

### Step 4: HTTP Service & Directory Brute-Forcing
1. Navigated to `http://192.168.56.102` in the browser, which displayed a welcome landing page ("Are You Ready To Play With Me...!")[cite: 3].
2. Ran `gobuster` with the dirb common wordlist to discover hidden directories:
   ```bash
   gobuster dir -u [http://192.168.56.102](http://192.168.56.102) -w /usr/share/wordlists/dirb/common.txt

 ### Step 5: QR Code Decoding & Credential Extraction
1. Navigated to `http://192.168.56.102/hidden_text` and clicked the "Thank you..." link to access a hidden page containing a QR code image[cite: 5, 6].
2. Downloaded the QR code image and uploaded it to an online decoder service (`zxing.org`) to extract the embedded text payload[cite: 6, 7].
3. **Decoded Output:** Revealed FTP credentials (`userftp`) and command structure (`HOST-10`, `USER=userftp`, `PASSWORD=Ptpassword`)[cite: 6, 7].

### Step 6: FTP Login & File Retrieval
Connected to the target FTP server using the recovered credentials and listed the remote directory contents[cite: 7]:
```bash
ftp 192.168.56.102  


