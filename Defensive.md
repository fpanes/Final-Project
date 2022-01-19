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

Two VMs on the network were vulnerable to attack due to poor security installation services and administration: Target 1 (192.168.1.110) and Target 2. (192.168.1.115). Target 1 has, however, been covered and attacked.

Attackers may get access to Target 1 through ports 80 and 22 since it is an Apache web server with SSH enabled. As a result of this, the following warnings have been issued:

### Monitoring the Targets

The amount of traffic to these services should be carefully controlled. To that end, we have implemented the following warnings:

#### Excessive HTTP Errors
Excessive HTTP Errors is implemented as follows:
  - **Metric**: 
    WHEN count() GROUPED OVER top 5 ‘http.response.status_code’
  - **Threshold**: IS ABOVE 400
  - **Vulnerability Mitigated**: Enumeration
  - **Reliability**: The alert is really reliable. When measuring by error codes 400 and higher, any normal or successful replies will be taken out. The 400+ codes indicate more significant client and server issues. Especially when these error codes are being created at such a rapid pace. It came through our wpscan.




#### HTTP Request Size Monitor
HTTP Request Size Monitor is implemented as follows:
  - **Metric**: WHEN sum() of http.request.bytes OVER all documents
  - **Threshold**: IS ABOVE 3500
  - **Vulnerability Mitigated**: Measurement of high-traffic occurrences, which may be a warning of an impending attack.
  - **Reliability**: This was highly reliable when John the Ripper was executed.
  


#### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:
  - **Metric**: WHEN max() OF system.process.cpu.total.pct OVER all documents
  - **Threshold**:IS ABOVE 0.5
  - **Vulnerability Mitigated**: John the Ripper
  - **Reliability**: Low and unreliable; the cpu usage monitor triggered even when not attacked.
  


### Suggestions for Going Further

- Each of the alerts listed above is for a distinct vulnerability/exploit. Recall that notifications merely identify harmful conduct and do not prevent it. Suggest a fix for each vulnerability/exploit reported in the warnings above. Implementing a blocklist, for example, is an effective defense against brute-force attacks.

The logs and warnings created during the evaluation indicate that this network is vulnerable to multiple active threats, as shown by the aforementioned alerts. In addition to monitoring for such attacks, the network should be hardened against them. To secure the network, the Blue Team recommends that IT execute the following fixes:
- Vulnerability 1: Weak and Easily Brute-Forced Passwords / SSH Login
  - Mitigation Techniques:
    - Set a custom SSH port
    - **Why It Works**: SSH is configured to listen on port 22 by default. Almost all cyber attackers are aware of this. Changing the default port 22, for example, to 846, adds an extra degree of protection through obscurity.
    - Implement SSH Private/Public Keys to login via SSH and disable passwords.
    - **Why It Works**: Each key consists of a huge integer with distinct mathematical features. The private key is kept on the computer from which you login, while the public key is kept in the.ssh/authorized keys file on each machine to which you want to login.
    - Allow Only Specific Clients (Connections)
    - **Why It Works**: executing the command "iptables -A INPUT -p tcp -s YourIP —dport 22 -j ACCEPT" You open the SSH port just to your IP address by defining a particular filter/rule in your firewall.
- Vulnerability 2: WordPress User Enumeration
  - Mitigation Techniques: 
    - "Texas" 2-Step: Disable Scans and Block User Enumeration via .htcaccess
    - **Why It Works**: This 2 step process will add layers of security by:
    - Adding a code snippet to your theme's functions.php file:

    - Adding a code snippet to your site's root.htcaccess file (the file must be created if it does not already exist)
  
- Vulnerability 3: Weak wp-config.php security implementation
  - Mitigation Techniques:
  - Adding adequate file security permissions to user files and protecting the wp-config.php file with a.htcaccess file
  - **Why It Works**: Setting up adequate file security permissions may have prevented us from reading the file when we ran the cat/nano command. This left the wp-config.php file with a password listed in plaintext.

  - Modify wp-config.php
  - **Why It Works**: It is safeguarded against accidental access or external attackers by generating a new config file outside of the WWW access directory.
  - Setup the correct file permissions for WP-Config.php to 400
  - **Why it Works**: By running chmod 400 on the file, users and groups will only be able to read it, while others will be unable to access it.
- Vulnerability 4: Python Privilege Escalation
  - Mitigation Techniques: 
    - Remove sudo access for python for the user.
    - **Why It Works**: When this access is removed, this technique of privilege escalation is no longer vulnerable.
