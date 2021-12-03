# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology


The following machines were identified on the network:

- Kali
  - **Operating System**: Kali Release 2020.1 / Kernel: Linux 5.4.0
  - **Purpose**: Attacker / pen test machine
  - **IP Address**: 192.168.1.90

- ELK
  - **Operating System**: Ubuntu 18.04
  - **Purpose**: Elasticsearch & Kibana Stack
  - **IP Address**: 192.168.1.100

- Target 1
  - **Operating System**: Debian GNU/Linux 8/v3.16.0-6
  - **Purpose**: WordPress host
  - **IP Address**: 192.168.1.110

- Target 2
  - **Operating System**: Debian GNU/Linux 8/v3.16.0-6
  - **Purpose**: WordPress host
  - **IP Address**: 192.168.1.115

- Capstone
  - **Operating System**: Ubuntu 18.04
  - **Purpose**: The Vulnerable Web Server
  - **IP Address**: 192.168.1.105

### Description of Targets

The target of this attack was: `Target 1` (192.168.1.110).

Due to inadequate security installation services and administration, two VMs on the network were exposed to attack: Target 1 (192.168.1.110) and Target 2. (192.168.1.115). However, only Target 1 has been covered and attacked.

Because Target 1 is an Apache web server with SSH enabled, attackers may get access via ports 80 and 22. As a result, the following alerts have been issued:

### Monitoring the Targets

The volume of traffic to these services should be closely monitored. To that purpose, we have implemented the following alerts:

#### Excessive HTTP Errors
Excessive HTTP Errors is implemented as follows:
  - **Metric**: 
    WHEN count() GROUPED OVER top 5 ‘http.response.status_code’
  - **Threshold**: IS ABOVE 400
  - **Vulnerability Mitigated**: Enumeration
  - **Reliability**: The alert is quite dependable. Any normal or successful responses will be filtered out when measuring by error codes 400 and above. The 400+ codes are client and server problems, which are more serious. Especially when these error codes are being generated at such a high rate. It discovered our wpscan.
