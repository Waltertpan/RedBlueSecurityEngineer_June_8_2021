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

!WPScan(/Images/WPScan.png)

### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33ellb80be759c4e844862482d
    - **Exploit Used**
      - WPScan to enumerate users
      - wpscan --url 192.168.1.110/wordpress/ --enumerate u

![SSHVuln](/Images/SSHVuln.png)

  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_