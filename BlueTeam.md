# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology
![Network Topology](/Images/Final_project_top.JPG)

The following machines were identified on the network:
- Kali
  - **Operating System**: Debian Kali 5.4.0
  - **Purpose**: The Penetration Tester
  - **IP Address**: 192.168.1.90
- Capstone
  - **Operating System**: Ubuntu 18.04
  - **Purpose**: The Vulnerable Web Server
  - **IP Address**: 192.168.1.105
- ELK
  - **Operating System**: Ubuntu 18.04
  - **Purpose**: The ELK (Elasticsearch and Kibana) Stack
  - **IP Address**: 192.168.1.100
- Target 1
  - **Operating System**: Linux
  - **Purpose**: WordPress Host
  - **IP Address**: 192.168.1.110

### Description of Targets

The target of this attack was: `Target 1` 192.168.1.110.

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors Monitor 

Excessive HTTP Errors Monitor is implemented as follows:
  - **Metric**: WHEN count() GROUPED OVER top 5 ‘http.response.status_code’
  - **Threshold**: IS ABOVE 400 FOR THE LAST 5 minutes
  - **Vulnerability Mitigated**: Brute Force attack and enumeration
  - **Reliability**: High reliability in detecting brute force and port scan attempts

#### HTTP Request Size Monitor
HTTP Request Size Monitor is implemented as follows:
  - **Metric**: WHEN sum() of http.request.bytes OVER all documents
  - **Threshold**: IS ABOVE 3500 FOR THE LAST 5 minutes
  - **Vulnerability Mitigated**: DDOS and Brute Force attack
  - **Reliability**: High reliabilty in detecting suspecious activity

#### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:
  - **Metric**: WHEN max() OF system.process.cpu.total.pct OVER all documents
  - **Threshold**: IS ABOVE 0.5 FOR THE LAST 5 minutes
  - **Vulnerability Mitigated**: Malicious software running in the background
  - **Reliability**: Low reliability and generated false negatives. Did not detect the back door installed

#### Port Scan Detection Monitor 
Port Scan Detection Monitor is implemented as follows:
  - **Metric**: WHEN count() GROUPED OVER top 5 ‘destination.packets’
  - **Threshold**: IS ABOVE 1500 FOR THE LAST 5 minutes
  - **Vulnerability Mitigated**: enumeration
  - **Reliability**: High reliability in detecting port scan attempts
![PortScanMonitor](/Images/PortScanMonitor.png)

#### Brute Force Monitor 
Brute Force Monitor is implemented as follows:
  - **Metric**: WHEN count() GROUPED OVER top 5 ‘system.auth.ssh.event’
  - **Threshold**: IS ABOVE 15 FOR THE LAST 2 minutes
  - **Vulnerability Mitigated**: Brute Force
  - **Reliability**: Medium reliability in detecting Brute Force attempts.
![BruteForceMonitor](/Images/BruteForceMonitor.png)

#### Privilege Escalation Monitor 
Privilege Escalation is implemented as follows:
  - **Metric**: WHEN count() GROUPED OVER top 5 ‘system.auth.sudo.tty’
  - **Threshold**: IS ABOVE 1 FOR THE LAST 5 minutes
  - **Vulnerability Mitigated**: Privilege Escalation 
  - **Reliability**: High reliability in detecting privilege escalation  attempts.
![PrivilegeEscalation](/Images/PrivilegeEscalation.png)

### Explaination of Monitors

#### Port Scan Detection Monitor
The detected the number of individual packets sent from 1 host in a short amount of time indicates a port scan has occurred.
![PortScanOccured](/Images/PortScanOccured.png)

#### Brute Force Monitor 
The number of failed SSH attempted in a short amount of time indicates a brute force attack has occured.
![SSHattempted](/Images/SSHattempted.PNG)

#### Privilege Escalation Monitor 
The number of TTY attempted iindicates a user privilege escalation attempt has occurred.
![TTYattempted](/Images/TTYattempted.png)