# BlueMoon: 2021 — VulnHub Machine Penetration Testing Report

* **Student Name:** Nurul Suhaila Binti Anissufian
* **Student ID:** 52215125568
* **Class:** L01-B01
* **Course:** Computer System Security
* ** Lab:** Lab Week 3: Vulnerable Machine Penetration Testing & Privilege Escalation
* **Target Machine:** BlueMoon: 2021 (VulnHub)
* **Assessment Date:** 4 November 2026

---

## Executive Summary

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
First, check the local network interface configuration and active IP address details using ifconfig to accurately determine the current subnet environment. 

![Step 1 - Identify the Local IP Address](Screenshot%202026-11-25%20235033.png)
Our IP address is 192.168.56.101 

## Step 2: Scan the Local Network for Targets 
Next, run an Nmap ping scan (-sn) across the local subnet range to locate the active IP address 
assigned to the Bluemoon virtual machine. 

![Step 2 - Scan the Local Network for Targets](Screenshot%202026-11-25%20235043.png)
Target IP address identified as: 192.168.56.1  
: 192.168.56.100 
: 192.168.56.102 
: 192.168.56.101 

## Step 3: Scan Open Ports and Services 
Run a comprehensive full TCP port scan (-sV -p-) to identify all active listening network services along with their exact running version details. 

![Step 3 - Scan Open Ports and Services](Screenshot%202026-11-25%20235052.png)
Three open target network service ports were discovered during the initial scan: 
Port 21: FTP (File Transfer Protocol service) 
Port 22: SSH (Secure Shell remote access) 
Port 80: HTTP (Apache Web Server instance) 

## Step 4: Check with HTTP service 
Go to to browser and navigate to http://192.168.56.102  

![Step 4- Check with HTTP service](screenshots/Screenshot%202026-11-25%20235109.png)

Directory Brute-Forcing :
Since the main home page displays no useful information, run Gobuster with a standard wordlist to search for hidden web directories. 

![Step 4 - Gobuster Directory Enumeration](Screenshot%202026-11-25%20235130.png)
Gobuster successfully discovers an unlinked hidden web directory path named /hidden_text hosted on the web server. 

## Step 5: Decode QR Code Image 

![Step 5a - Hidden Maintenance Page](Screenshot%202026-11-25%20235143.png)
Visit (http://192.168.56.107/hidden_text) inside a browser and click the "Thank you..." link to open a hidden page containing a QR code image.

![Step 5b - QR Code Image](Screenshot%202026-11-25%20235156.png)
Download and save the displayed QR code image directly onto the local filesystem 
working directory. 

![Step 5c - Decoded QR Code Credentials](Screenshot%202026-11-25%20235206.png)
Upload the saved image file to an online decoder service (zxing.org) to extract and reveal the hidden plain text credentials embedded inside.

## Step 6: FTP Login & File Retrieval 
Connect directly to the target FTP server by login and enter the password from image that have been decode and list all directory contents stored inside the remote folder. 

![FTP Login and Directory Listing](Screenshot%202026-11-25%20235216.png)

![Download information.txt and p_lists.txt via FTP](Screenshot%202026-11-25%20235228.png)

## Step 7: Inspect Retrieved Files
To read through the contents of both downloaded text files to collect target user notes and potential password entries for subsequent authentication steps.
![Contents of information.txt](Screenshot%202026-11-25%20235237.png)

![Contents of p_lists.txt Wordlist](Screenshot%202026-11-25%20235248.png)
File p_lists.txt contains a custom wordlist ready to be utilized for future password brute-force login attempts against system services. 

## Step 8: Brute-Force SSH Credentials 
Run Hydra using the retrieved custom password list (p_lists.txt) to target the target user account robin over the active SSH service protocol. 

![Hydra SSH Brute-Force for User robin](Screenshot%202026-11-25%20235257.png)

## Step 9: SSH Login & First Flag Retrieval 
Establish a secure remote command line connection to the target system via SSH using the valid authentication credentials discovered for user Robin. 
![SSH Login to BlueMoon as robin](Screenshot%202026-11-25%20235308.png)

![List Files in robin Home Directory](Screenshot%202026-11-25%20235318.png)
List all home directory files including hidden items, then print the file contents of user1.txt to capture the first user flag. 

![Read user1.txt Flag](Screenshot%202026-11-25%20235324.png)

## Step 10: Check Sudo Permissions
Execute sudo -l to inspect all configured sudo command execution privileges assigned to the current user account robin without requiring administrative passwords.
![Check Sudo Permissions for robin](Screenshot%202026-11-25%20235330.png)

## Step 11: Execute Script for Shell Spawning 
Navigate into the target project folder directory, inspect the script source code, and execute the script directly as user jerry using sudo. 

![Inspect feedback.sh Script Contents](Screenshot%202026-11-25%20235339.png)

![Run feedback.sh as jerry Using sudo](Screenshot%202026-11-25%20235350.png)

## Step 12: Verify User Identity & Second Flag 
Confirm the successful user identity context switch, navigate directly into Jerry's home directory path, and open the second user flag file (user2.txt). 

![Shell as jerry and Read user2.txt Flag](Screenshot%202026-11-25%20235412.png)

## Step 13: Upgrade Shell Interactivity 
Spawn a fully interactive Bash pseudo-terminal prompt using Python's built-in pty module to stabilize interactive terminal session input control and formatting.

![Spawn Interactive Bash Shell with Python PTY](Screenshot%202026-11-25%20235427.png)

## Step 14: Inspect User Groups 
Run the system id command to inspect all group memberships and assigned security privileges for user account jerry. 

![Check jerry Group Membership and Docker Images](Screenshot%202026-11-25%20235436.png)
User account jerry is verified as an active member belonging to the local administrative docker group. 

## Step 15: Docker Exploitation to Root 
Check available local Docker container images and launch an Alpine container, mounting the host root filesystem (/) to /mnt to access an unconstrained root shell (chroot).

![Docker Permission Denied for Non-Docker User](Screenshot%202026-11-25%20235444.png)

## Step 16: Verify Root Status & Final Flag 
Confirm root administrative privilege level using id, navigate directly into the /root target directory, and print the contents of root.txt to finish the challenge.

![Escalate to Root via Docker Mount and Read root.txt Flag](Screenshot%202026-11-25%20235458.png)

![Root Flag Output in root.txt](Screenshot%202026-11-25%20235506.png)
